---
name: chart
description: Agricultural statistics and chart CLI via nong. Trigger on ANOVA, Duncan MRT, 方差分析, 显著性, treatment groups, error bars, or bar chart generation.
---

# Chart

Use `nong` for statistical analysis and implemented figure generation.

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
nong chart analyze <groups.json> [--alpha 0.05] [--json]
nong chart anova <groups.json> [--json]
nong chart duncan <groups.json> [--alpha 0.05] [--json]
nong chart bar <groups.json> -o <out.png> [--title <text>] [--ylabel <text>] [--error sem|none] [--no-significance] [--json]
```

## Dispatch

1. For one-shot agricultural treatment analysis, run `nong chart analyze <groups.json> --json`.
2. For ANOVA only, run `nong chart anova <groups.json> --json`.
3. For Duncan multiple comparison only, run `nong chart duncan <groups.json> --json`.
4. For a publication-style bar chart, run `nong chart bar <groups.json> -o <out.png> --json`.
5. If data starts in Excel, use `excel to-groups --raw` first.
6. If the user asks for line, scatter, pie, box, histogram, heatmap, radar, or combined panels, say it is not implemented in the current `nong` CLI.

## Input Contract

Use grouped JSON:

```json
{
  "Control": [1.2, 1.3, 1.1],
  "Treatment": [2.0, 2.2, 2.1]
}
```

Each group needs at least two numeric observations. Treat `E006 validation_failed` as a data problem to fix before analysis.
