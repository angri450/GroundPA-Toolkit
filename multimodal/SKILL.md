---
name: multimodal
description: OCR and image-structure QA via nong. Trigger on OCR, PaddleOCR, scanned image, image-to-Word, chart visual QA, or multimodal document extraction.
---

# Multimodal / OCR

Use `nong ocr` as the only GroundPA entrypoint for OCR and image-structure analysis. Do not route GroundPA through Python OCR libraries or custom OCR wrappers.

## Nong CLI Preflight

Claude Plugin Marketplace installs the skills, not the `nong` CLI. Verify the CLI before OCR work:

```powershell
nong commands --json
```

If `nong` is missing, install or update:

```powershell
dotnet tool install --global Angri450.Nong.Cli
dotnet tool update --global Angri450.Nong.Cli
```

If the .NET host says no compatible framework was found, use Nong 3.2.3+ or set `DOTNET_ROLL_FORWARD=LatestMajor` for the current shell and retry.

## OCR Prerequisites

Run once before OCR work:

```powershell
nong ocr check-env --json
```

Cloud OCR and image-to-Word require this environment variable:

```powershell
$env:PADDLEOCR_ACCESS_TOKEN = "<access-token>"
```

Get the cloud token from the PaddleOCR/AI Studio access-token page: `https://aistudio.baidu.com/account/accessToken`. If the user has no token, use local OCR only after its smoke test passes.

Do not write real credentials into repository files, logs, or examples.

## Implemented Commands

Exposed CLI commands:

```powershell
nong ocr check-env --json
nong ocr analyze-image <image.png> -o <analysis-dir> --json
nong ocr cloud <image-or.pdf> -o <ocr-out-dir> --json
nong ocr to-word <image-or.pdf> -o <out.docx> --json
nong ocr models --json
nong ocr install-model pp-ocrv5-mobile --json
nong ocr install-model pp-ocrv5-mobile --dry-run --json
```

Gated local path:

```powershell
nong ocr local <image.png> --json
```

## Dispatch

1. For environment status, run `nong ocr check-env --json`.
2. For chart or diagram visual QA, run `nong ocr analyze-image <image.png> -o <analysis-dir> --json`.
3. For text/document recognition through PaddleOCR cloud, require `PADDLEOCR_ACCESS_TOKEN`, then run `nong ocr cloud <input> -o <ocr-out-dir> --json`.
4. For Word output from a scan or PDF, require `PADDLEOCR_ACCESS_TOKEN`, then run `nong ocr to-word <input> -o <out.docx> --json`.
5. For model inventory, run `nong ocr models --json`.
6. For local deployment planning, run `nong ocr install-model pp-ocrv5-mobile --dry-run --json`; it should report `noPython: true` and domestic NuGet mirror options.
7. Use `ocr local` after `check-env` reports `localDotNetPpOcrV5.status=ok` and an actual image smoke test exits 0.

## Boundaries

`ocr analyze-image` performs structural image QA and does not recognize text. It checks layout-oriented signals such as whitespace, content bounds, and visible content regions.

`ocr cloud` and `ocr to-word` require `PADDLEOCR_ACCESS_TOKEN`.

`ocr local` is an implemented CLI entrypoint through Nong's pure .NET PP-OCRv5 runtime. Do not describe it as a stable OCR route unless the local environment and a real image smoke test have passed.

Treat `status: "error"` as failed. Do not mask dependency or not-implemented errors as success.
