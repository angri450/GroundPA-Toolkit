---
name: pdf
description: Local PDF document slicing through nong. Trigger on PDF preflight, selectable-text PDF extraction, hybrid/scan routing, PDF page rendering, embedded PDF image extraction, content.nongmark, page/bbox evidence, or PDF-to-AI-readable document packages.
---

# PDF

Use `nong pdf` as the deterministic local PDF entrypoint. GroundPA routes, reads JSON/slice outputs, and reports evidence; it does not recreate PDF parsing with Python, Pandoc, MinerU, or ad hoc scripts.

The primary readable artifact is `content.nongmark`, not plain Markdown. `preview/content.md` is a lossy compatibility preview only.

## Nong CLI Preflight

Claude Plugin Marketplace installs the skills, not the `nong` CLI. Before the first Nong command in a session, run:

```powershell
nong commands --json
```

If `nong` is missing, install or update:

```powershell
dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
dotnet tool update --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

Use Nong 3.2.4+ for PDF workflows; `pdf check`, `pdf dissect`, `pdf render`, and `pdf images` are part of that command surface. If the .NET host says no compatible framework was found, update to Nong 3.2.4+ or set `DOTNET_ROLL_FORWARD=LatestMajor` for the current shell and retry.

## Default Workflow

1. Always classify the PDF first:

```powershell
nong pdf check <file.pdf> --json
```

Use `classification`, `recommendedMode`, `hasTextLayer`, `textCharsPerPage`, `imageCoverageRatio`, and `warnings` to choose the path.

2. For selectable text PDFs, local slicing is the default:

```powershell
nong pdf dissect <file.pdf> --output <slice-dir> --mode auto --json
```

Read at least:

- `<slice-dir>/content.nongmark`
- `<slice-dir>/content.jsonl`
- `<slice-dir>/structure.json`
- `<slice-dir>/format.json`
- `<slice-dir>/diagnostics/check.json`

3. For page images or visual inspection:

```powershell
nong pdf render <file.pdf> --output <pages-dir> --dpi 150 --json
```

4. For embedded image provenance:

```powershell
nong pdf images <file.pdf> --output <assets-dir> --json
```

5. For scan PDFs without a useful text layer, use `pdf dissect --mode ocr` only after local OCR runtime is installed and has passed a real smoke test:

```powershell
nong ocr check-env --json
nong ocr install-model pp-ocrv5-mobile --source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json --json
nong pdf dissect <scan.pdf> --output <slice-dir> --mode ocr --json
```

Local OCR runtime bundles track the CLI version (`Angri450.Nong.OcrRuntime.*` 3.2.4 for Nong 3.2.4). Right after a fresh NuGet release, domestic mirrors can lag; if Huawei has not synced the runtime package yet, use NuGet.org explicitly or report mirror lag instead of falling back silently.

Local OCR is text recognition only. It does not provide cloud-grade layout labels, table structure, Word formatting, or reliable cross-page reconstruction. For page-faithful PDF-to-Word/NongMark output, prefer readable-text `pdf dissect` first; use cloud OCR/to-word when scan layout, tables, or page alignment matter and a token is available.

## Evidence Rules

- Do not answer PDF layout or extraction questions from copied plain text alone.
- Use `content.nongmark` for AI-readable text with page, bbox, source, font, size, and block IDs.
- Use `content.jsonl` as the block-level source of truth for page/bbox/source evidence.
- Use `diagnostics/check.json` to explain why a PDF was routed as `text`, `hybrid`, or `scan`.
- If `preview/content.md` exists, treat it as lossy preview and say so when precision matters.
- If `pdf check` reports `scan`, do not claim local parsing can recover full layout unless OCR mode actually succeeds and the output contains blocks.
- If cloud OCR is available, `nong ocr cloud` or `nong ocr to-word` can be stronger for full PDF layout/table reconstruction, but the cloud key is not a prerequisite for basic local PDF slicing.

## Output Contract

`pdf dissect` should produce:

```text
slice-dir/
  manifest.json
  document.json
  content.jsonl
  structure.json
  format.json
  content.nongmark
  preview/content.md
  pdf-assets-manifest
  diagnostics/check.json
  diagnostics/reading-order.json
  diagnostics/warnings.json
```

For a non-empty text PDF, do not treat the command as successful unless `content.nongmark`, `content.jsonl`, `document.json`, `structure.json`, `format.json`, and the asset manifest under the slice asset directory exist and are non-empty.

## Boundaries

Implemented local commands:

```powershell
nong pdf check <file.pdf> --json
nong pdf dissect <file.pdf> --output <slice-dir> --mode auto --json
nong pdf render <file.pdf> --output <pages-dir> --dpi 150 --json
nong pdf images <file.pdf> --output <assets-dir> --json
```

Do not use Pandoc as a PDF parser. Do not require Python, pip, MinerU, or a Pandoc executable on the client machine.

`pdf dissect --mode auto` is reliable for selectable text PDFs in Nong 3.2.4+. Hybrid mode preserves native text and embedded image evidence, while image-region OCR/layout enrichment is still limited. OCR mode depends on Nong's local PP-OCRv5 runtime and should be treated as text extraction, not full document reconstruction.

## Error Contract

Always pass `--json` when output feeds another tool or model decision. Treat `status: "error"` as failed.

Common codes:

- `E001 file_not_found`: fix the input path.
- `E002 unsupported_format`: the input is not a valid `.pdf`.
- `E005 dependency_missing`: install/check the PDF/OCR native runtime; for OCR mode run `nong ocr install-model pp-ocrv5-mobile --json`.
- `E006 validation_failed`: fix mode, DPI, or output path.
- `E007 read_failed`: PDF exists but parsing/extraction failed; try render or cloud OCR if appropriate.
- `E008 write_failed`: output artifacts were not created or are empty.
- `E004 internal_error`: unexpected bug; keep stderr/stdout and command JSON for diagnosis.
