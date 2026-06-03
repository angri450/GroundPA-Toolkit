---
name: inspect
description: Agricultural paper inspection and generation via nong. Trigger on paper diagnosis, 论文诊断, reference check, 参考文献, evidence chain, gap grade, data requirements, or generating a paper draft from JSON spec.
---

# Inspect

Use `nong inspect` for paper-level semantic work that is implemented in the current CLI.

## Prerequisites

Run once before work:

```powershell
nong commands --json
```

If `nong` is missing, tell the user to install:

```powershell
dotnet tool install --global Angri450.Nong.Cli
```

## Implemented Commands

```powershell
nong inspect diagnose <paper.txt> [--json]
nong inspect refs <paper.txt> [--json]
nong inspect write-paper <spec.json> -o <out.docx> [--json]
```

## Dispatch

1. For full paper quality diagnosis, run `nong inspect diagnose <paper.txt> --json`.
2. For references and citation risk, run `nong inspect refs <paper.txt> --json`.
3. For a Word paper draft, prepare a paper spec JSON and run `nong inspect write-paper <spec.json> -o <out.docx> --json`.
4. If the source is `.docx`, first use `nong word read <file.docx>` to create plain text, then pass the text file to inspect.
5. If the user asks for classify, structure, varplan, evidence, data-req, gap, semantics, official writing, or letter writing as standalone commands, say it is not implemented in the current `nong` CLI.

## Paper Spec

Use this minimal shape for `write-paper`:

```json
{
  "title": "Title",
  "abstract": "Abstract text.",
  "keywords": "keyword1; keyword2",
  "sections": [
    {
      "heading": "1 Introduction",
      "level": 1,
      "body": ["Paragraph one.", "Paragraph two."]
    }
  ],
  "references": ["[1] Author. Title. Journal, 2026."]
}
```

`sections[].heading` is required. `level` must be 1-3. Treat `E006 validation_failed` as a spec problem.
