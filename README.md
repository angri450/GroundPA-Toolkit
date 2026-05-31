# GroundPA Toolkit

OpenXML-native, .NET-first Claude Code skills. 15 skills covering Office document generation, shell scripting, .NET development, GitHub operations, and skill lifecycle management — all powered by deterministic NuGet packages and CLI tools.

## Philosophy

Model capacity is for semantic work — mining, synthesizing, writing. Deterministic work (validate, scan, package, generate, render) ships as NuGet packages. Skill folders are read-only at distribution time; all runtime data lives under a unified workspace.

## Skills

### Office & Document Generation

| Skill | Does | NuGet |
|-------|------|-------|
| **word** | Academic papers, theses, reports — three-line tables, formula numbering, TOC, template engine | `Angri450.Nong.Docx` |
| **pptx** | Defense presentations, research reports, lecture slides — 10 themes, fluent SlideBuilder | `Angri450.Nong.Pptx` |
| **excel** | Spreadsheets, financial models, dashboards — formulas, validation, conditional formatting | `Angri450.Nong.Excel` |
| **chart** | ANOVA, Duncan MRT, publication-quality bar charts with significance labels | `Angri450.Nong.Chart` |
| **diagram** | Flowcharts (Sugiyama), network graphs (force-directed), phylogenetic trees (Newick), bioicons | `Angri450.Nong.Diagram` |
| **multimodal** | OCR (PaddleOCR-VL cloud + local CPU), document-to-Markdown, document-to-Word | `Angri450.Nong.MultiModal` |

### Shell Scripting

| Skill | Does |
|-------|------|
| **bash** | Quoting, arrays, `set -e`, trap, Git Safety Protocol, sandbox-safe patterns |
| **powershell** | PS 7+: cmdlets, modules, Pester, PSScriptAnalyzer, credential security |

### Developer Tools

| Skill | Does |
|-------|------|
| **dotnet** | C#, MSBuild, ASP.NET Core, EF Core, MAUI, performance diagnostics |
| **github** | git + gh CLI: commits, PRs, issues, releases |
| **ghproxy** | GitHub URL acceleration for restricted-network environments |
| **nuget** | Package management: install, update, pack, publish |
| **ilspycmd** | Decompile .NET assemblies to C# — inspect DLLs, extract API surface |

### Communication & Meta

| Skill | Does | NuGet |
|-------|------|-------|
| **email** | ClawEmail mail-cli: inbox, forward, search, attachments | — |
| **skill-manager** | Validate, scan, package, eval, scaffold — full skill lifecycle CLI | `Angri450.Nong.Skill.Manager` |

## Workspace

All runtime data lives under one tree:

```
~/Documents/GroundPA Toolkit Workplace/
├── word/DocxWriter/          # Word project
├── pptx/PptxWriter/          # PowerPoint project
├── excel/ExcelWriter/        # Excel project
├── chart/ChartWriter/        # Chart project
├── diagram/DiagramWriter/    # Diagram project
├── multimodal/OcrTask/       # OCR project
├── skill-manager/            # Evals, session records, build artifacts
└── output/                   # Generated files
    └── <timestamp>+<project>+<seq>/
```

Each session modifies only `Program.cs` in the relevant workspace. Output lands in a timestamped subdirectory under `output/`.

## Install

### One-liner

```bash
git clone https://github.com/angri450/GroundPA-Toolkit.git /tmp/groundpa && \
  cp -r /tmp/groundpa/* ~/.claude/skills/ && \
  rm -rf /tmp/groundpa && \
  dotnet tool install --global Angri450.Nong.Skill.Manager
```

**Prerequisites:** [.NET SDK](https://dotnet.microsoft.com/download), [Git](https://git-scm.com/).

| You're on | Instead of `cp -r` |
|-----------|--------------------|
| Windows PowerShell | `Copy-Item /tmp/groundpa/* -Destination ~/.claude/skills/ -Recurse` |
| Windows (WSL / Git Bash) | Same as above |

### Marketplace (recommended)

GroundPA Toolkit ships with its own Claude Code marketplace. Friends install in two commands:

```bash
claude plugin marketplace add angri450/GroundPA-Toolkit
claude plugin install groundpa-toolkit@groundpa-marketplace
```

Then reload in Claude Code:

```text
/reload-plugins
```

Skills use the `groundpa-toolkit:` namespace:

```text
/groundpa-toolkit:word
/groundpa-toolkit:pptx
/groundpa-toolkit:chart
```

## Architecture

Skill folders ship read-only. Deterministic work runs through NuGet packages (`Angri450.Nong.*`). The skill-manager global tool handles validation, security scanning, packaging, and evaluation. Progressive disclosure keeps SKILL.md lean — deep reference material lives in `references/`.

See [`skill-manager/SKILL.md`](skill-manager/SKILL.md) for full conventions.

## Marketplace

This repo is its own marketplace. Anyone can register it once and install:

```bash
claude plugin marketplace add angri450/GroundPA-Toolkit
claude plugin install groundpa-toolkit@groundpa-marketplace
```

To submit to community directories for wider discovery:

1. Validate locally: `claude plugin validate .`
2. Submit to Anthropic Community Marketplace via the form on claude.ai
3. Or submit a PR to `kossakovsky/cc-plugins` for third-party listing

## License

Apache-2.0
