# Existing Document Editing

Use this reference when the user provides an existing Word file and asks to change formatting, convert a contract, extract a template, repair a document, or compare Nong against raw Word COM automation.

## Boundary: `.doc` vs `.docx`

`nong word` works on `.docx` OpenXML. Legacy binary `.doc` must be converted before deterministic Nong processing.

Recommended stance:

1. Treat `.doc -> .docx` as a boundary conversion step.
2. Use installed Word COM or LibreOffice only for that conversion when no pure converter is available.
3. After conversion, return immediately to `nong word`.
4. Do not use COM as the main reading/editing engine.

For Word COM conversion, read [com-automation.md](com-automation.md). Prefer hidden Word, unique output paths, `SaveAs2(..., 16)`, explicit document close, `Quit()`, COM release, and no blanket `Stop-Process WINWORD -Force`.

## Real-Case Pipeline

For a user-supplied legacy contract or form:

```powershell
# 1. Convert .doc to .docx by an explicit boundary step.
# 2. Inspect and slice.
nong word dissect contract.docx --output contract.slice --json
nong word preview contract.docx --json
nong word validate contract.docx --json
nong word fonts contract.docx --json
nong word styles contract.docx --json

# 3. Repair converted legacy OOXML.
nong word fix-order contract.docx -o contract.fixed.docx --json
nong word validate contract.fixed.docx --json

# 4. Rebuild styling if needed, then validate again.
nong word rebuild contract.fixed.docx -o contract.rebuilt.docx --json
nong word validate contract.rebuilt.docx --json
nong word preview contract.rebuilt.docx --json
```

Do not stop after `read`. For table-heavy contracts, `content.md` often shows text but not the visible contract layout. Use `format.json`, `content.jsonl`, `structure.json`, `fonts`, `styles`, `preview`, and `validate`.

## Legacy Word/WPS Artifacts

Real converted `.doc` files may contain schema-invalid but Word-tolerated XML. `word fix-order` is the first repair tool.

Known artifacts handled by recent Nong builds:

- false `w:noWrap w:val="0"` in table cells
- table-row `tblPrEx` ordering
- section `cols` ordering
- paragraph `framePr` ordering
- style `next` ordering
- invalid `tblStyle` children inside table-style `tblPr`
- style-reference diagnosis where legacy numeric `styleId` maps to a style name such as `Normal`

After repair, always run:

```powershell
nong word validate fixed.docx --json
nong word preview fixed.docx --json
```

Treat `validate` errors as blocking. Treat `preview` warnings as quality or formatting issues that may still need user-facing explanation.

## Contract Transformations

For "contract to official document", "contract to paper", or "extract contract template":

- Slice the source first.
- Extract parties, project name, fees, dates, term, confidentiality, acceptance, IP ownership, and signature/contact tables.
- Produce explicit artifacts instead of only prose:
  - repaired source DOCX
  - extracted field JSON
  - reusable template DOCX
  - filled template DOCX
  - paper draft DOCX when requested
  - official-document draft DOCX when requested
  - report with commands, logs, pass/fail status, and limitations

Use `nong inspect write-paper` for paper-style drafts. For official-document drafts, current Nong source has library support (`GongWenFormatter`, `OfficialDocWriter`), but the CLI does not yet expose a first-class `write-official` or `format-gongwen` command. If the CLI cannot do it directly, say that clearly and use available library-backed tooling only when the environment supports it.

## Consumer Feedback Response

The practical complaint was valid: a prior session fell back to raw COM and made Nong look absent. The correct GroundPA behavior is:

- Acknowledge that `.doc` conversion may require Word/LibreOffice.
- Do not continue with naked COM after conversion.
- Use Nong for deterministic inspection, repair, edits, generation, validation, and reporting.
- Surface product gaps instead of pretending they are solved.

Current remaining gap:

- Nong needs a first-class existing-document editing API/CLI that behaves like `DocumentReader -> rule matcher -> DocumentEditor -> writer`.
- Until that exists, use `dissect`, `fix-order`, `rebuild`, `add`, `fill`, and `inspect write-paper` as composable operations, with explicit artifacts and validation.

## Success Criteria

For real user documents, do not call the task done until:

- Source was copied or converted without modifying the original.
- Every generated DOCX has an explicit output path.
- `word validate` passes for deliverable DOCX files.
- `word preview` diagnostics are reviewed.
- A report records command outcomes and remaining limitations.
