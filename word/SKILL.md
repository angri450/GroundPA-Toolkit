---
name: word
description: Word document CLI operations via nong. Trigger on .docx, read Word text, preview structure, fill templates, rebuild styles, or inspect Word output.
---

# Word

Use `nong` as the only deterministic Word entrypoint. Do not scaffold temporary .NET projects for Word tasks.

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

Use only these commands in this skill:

```powershell
nong word read <file.docx> [--json]
nong word preview <file.docx> [--json]
nong word fill <template.docx> <data.json> -o <out.docx> [--json]
nong word rebuild <file.docx> -o <out.docx> [--json]
```

## Dispatch

1. For extracting text from `.docx`, run `nong word read <file> --json`.
2. For structural diagnosis, warnings, and OOXML preview, run `nong word preview <file> --json`.
3. For template filling, prepare a data JSON file and run `nong word fill <template> <data.json> -o <out.docx> --json`.
4. For style cleanup, run `nong word rebuild <file> -o <out.docx> --json`.
5. If the user asks for image extraction, style listing, font listing, validation, merge, or format fingerprinting, say that command is not implemented in the current `nong` CLI.

## Contract

Always use `--json` when the result will feed another tool or model decision. Treat `status: "error"` as failed even if text output looks useful. Use `artifacts.docx` as the generated file path.

## Paper Work

For paper diagnosis, reference checks, or paper generation, use the `inspect` skill, not Word.
