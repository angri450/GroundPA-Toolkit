---
name: word
description: Word document workflows through nong. Trigger on .doc/.docx conversion handoff, DOCX reading, formatting/layout inspection, NongMark slicing, existing-document repair, template fill, document edits, validation, merge, protection, comments, images, fonts, or Word-to-paper/official-document preparation.
---

# Word

Use `nong` as the deterministic Word entrypoint. GroundPA decides the workflow, prepares small specs, reads JSON/slice outputs, and reports evidence; it does not recreate DOCX parsing or generation logic in ad hoc scripts.

Do not answer layout or formatting questions from plain text alone. Do not use desktop Word COM automation as the normal editing path.

## Default Workflow

1. Verify the command surface when the session is new or behavior seems stale:

```powershell
nong commands --json
```

2. For complex existing `.docx`, layout questions, table-heavy contracts, or "does this look right" requests, start with a slice:

```powershell
nong word dissect <file.docx> --output <slice-dir> --json
```

Inspect at least:

- `<slice-dir>/format.json`
- `<slice-dir>/content.jsonl`
- `<slice-dir>/structure.json`

3. Add targeted inventory/diagnostic commands as needed:

```powershell
nong word fonts <file.docx> --json
nong word styles <file.docx> --json
nong word preview <file.docx> --json
nong word validate <file.docx> --json
```

4. For dirty or legacy-converted DOCX, repair before downstream edits:

```powershell
nong word fix-order <file.docx> -o <fixed.docx> --json
nong word validate <fixed.docx> --json
```

5. Write to explicit output paths with `-o`. Never overwrite the user's source unless they explicitly request that.

## Evidence Rules

- `word read` is text-only evidence. It cannot prove fonts, font size, line spacing, indentation, alignment, table borders, margins, captions, or visual layout.
- For formatting/layout claims, cite facts from `format.json`, `content.jsonl`, `structure.json`, `fonts`, `styles`, `preview`, or `validate`.
- If the first extraction was plain text, say so and run the format-oriented path before judging.
- Schema-valid does not mean visually ideal. Report remaining `preview` warnings separately from `validate` errors.

## Existing Documents

For user-supplied contracts, old Word files, table-heavy forms, or "modify this document's formatting", read [references/existing-document-editing.md](references/existing-document-editing.md).

Core stance:

- `.doc` requires a conversion handoff before OpenXML work. Use Word/LibreOffice conversion only as a boundary step, then return to `nong word`.
- Existing `.docx` should be inspected, repaired, edited through CLI/library-backed operations, and validated.
- COM is an escape hatch, not the main implementation.

## Reference Routing

Load only the reference needed for the task:

- [references/read-word.md](references/read-word.md): reading, slicing, formatting evidence, assets, comments, revisions.
- [references/write-word.md](references/write-word.md): template fill, add operations, merge, protect, embed fonts, repair, validation after writes.
- [references/api-reference.md](references/api-reference.md): exact command syntax and JSON spec shapes.
- [references/existing-document-editing.md](references/existing-document-editing.md): `.doc` handoff, legacy DOCX repair, real-case contract workflow, current product gaps.
- [references/com-automation.md](references/com-automation.md): only when installed Microsoft Word must be driven.
- [references/workspace-setup.md](references/workspace-setup.md): case workspace layout and artifact organization.
- [references/paper-analysis.md](references/paper-analysis.md): when Word output feeds inspect/paper workflows.

## Writing And Editing

Use canonical nested add commands:

```powershell
nong word add paragraph <file.docx> --spec paragraph.json -o <out.docx> --json
nong word add table <file.docx> --spec table.json -o <out.docx> --json
nong word add image <file.docx> --src fig.png --caption "Figure 1" -o <out.docx> --json
nong word add comment <file.docx> --text "Review note" -o <out.docx> --json
nong word add math <file.docx> --latex "E=mc^2" --display -o <out.docx> --json
```

Use `--after <blockId>` only after `word dissect --output` has identified the insertion point.

Use `word add-*` flattened aliases only for compatibility with older scripts; do not teach them as the canonical form.

## COM Escape Hatch

Use desktop Word COM only when all are true:

1. The user explicitly asks to drive installed Word, or the task requires Word's visual/layout engine.
2. `nong` cannot provide the needed fact or transformation.
3. The environment is Windows with Word installed.
4. You can isolate outputs and clean COM objects safely.

Before writing any COM script, read [references/com-automation.md](references/com-automation.md). Do not blanket-kill `WINWORD` without explicit user approval.

## Error Contract

Always pass `--json` when output feeds another tool or model decision. Treat `status: "error"` as failed.

Common codes:

- `E001 file_not_found`: fix input path; do not continue from stale output.
- `E003 missing_argument`: supply required `--spec`, `--text`, `--latex`, `--src`, or `-o`.
- `E006 validation_failed`: repair the spec, format description, or document validation issue before retrying.

Generated DOCX paths should be explicit with `-o`; use `artifacts.docx` only when no better user-facing path is available.
