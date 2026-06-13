---
name: excel
description: Excel CLI operations via nong. Trigger on .xlsx, worksheet listing, table reading, extracting data, or converting treatment/value columns into grouped JSON for statistics.
---

# Excel

Use `nong` for deterministic Excel reads and data preparation. Do not claim workbook creation, formatting, formulas, dashboards, or pivot tables in 2.0.0.

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
nong excel sheets <file.xlsx> [--json]
nong excel read <file.xlsx> [--sheet <name>] [--range <A1:D20>] [--json]
nong excel to-groups <file.xlsx> --group <col> --value <col> [--sheet <name>] [--json]
nong excel to-groups <file.xlsx> --group <col> --value <col> --raw > groups.json
```

## Dispatch

1. To list worksheets, run `nong excel sheets <file> --json`.
2. To inspect data, run `nong excel read <file> --json`; add `--sheet` and `--range` when known.
3. To prepare agricultural experiment data for statistics, run `nong excel to-groups ... --raw > groups.json`.
4. Feed raw grouped JSON directly into `nong chart analyze`, `anova`, `duncan`, or `bar`.
5. If the user asks to create or style Excel files, say it is not implemented in the current `nong` CLI.

## Groups JSON

`chart` commands expect:

```json
{
  "Control": [1.2, 1.3, 1.1],
  "Treatment": [2.0, 2.2, 2.1]
}
```

Use `--raw` for pipeline files. Use `--json` for model-readable reports.
