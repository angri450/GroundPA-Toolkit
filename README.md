# GroundPA Toolkit

GroundPA Toolkit 2.0.0 is a Nong CLI-first Claude Code skill set for agricultural paper and document workflows.

The skill layer no longer asks the model to scaffold temporary .NET projects for normal Office work. It routes requests to the deterministic `nong` CLI, then lets the model read JSON output, fix inputs, and compose the next step.

## Design

Model capacity is for semantic work: choosing workflows, drafting specs, interpreting diagnostics, and writing. Deterministic work runs through .NET tools:

```powershell
dotnet tool install --global Angri450.Nong.Cli
nong commands --json
```

The skill-manager tool remains the lifecycle tool for validating, scanning, packaging, and evaluating skills:

```powershell
dotnet tool install --global Angri450.Nong.Skill.Manager
```

## Implemented Nong Skills

Only implemented `nong` commands are exposed as 2.0.0 skills.

| Skill | Implemented Commands |
|-------|----------------------|
| `word` | `word read`, `word preview`, `word fill`, `word rebuild` |
| `inspect` | `inspect diagnose`, `inspect refs`, `inspect write-paper` |
| `excel` | `excel sheets`, `excel read`, `excel to-groups` |
| `chart` | `chart analyze`, `chart anova`, `chart duncan`, `chart bar` |
| `diagram` | `diagram flowchart`, `diagram network` |
| `genre` | `genre list`, `genre show` |
| `icons` | `icons list`, `icons search` |

PPTX and OCR are not exposed in 2.0.0 because the current `nong` CLI marks those commands as stubs.

## Other Skills

| Skill | Purpose |
|-------|---------|
| `bash` | Bash quoting, arrays, error handling, and sandbox-safe patterns |
| `powershell` | PowerShell cmdlets, modules, error handling, and credential safety |
| `dotnet` | C#, MSBuild, ASP.NET Core, EF Core, MAUI, diagnostics, and NuGet workflows |
| `github` | `git` and `gh` CLI workflows |
| `gitee` | Gitee and Gitee MCP workflows |
| `ghproxy` | GitHub URL acceleration for restricted-network environments |
| `nuget` | Package installation, packing, and publishing |
| `ilspycmd` | .NET assembly decompilation |
| `email` | ClawEmail mail-cli workflows |
| `skill-manager` | Skill validation, security scan, packaging, evals, and scaffolding |

## Common Workflows

### Word

```powershell
nong word read paper.docx --json
nong word preview paper.docx --json
nong word fill template.docx data.json -o out.docx --json
nong word rebuild dirty.docx -o clean.docx --json
```

### Paper Inspection

```powershell
nong inspect diagnose paper.txt --json
nong inspect refs paper.txt --json
nong inspect write-paper spec.json -o paper.docx --json
```

### Excel to Statistics to Chart

```powershell
nong excel to-groups data.xlsx --group Treatment --value Yield --raw > groups.json
nong chart analyze groups.json --json
nong chart bar groups.json -o fig.png --json
```

### Diagrams

```powershell
nong diagram flowchart flow.json -o flow.png --json
nong diagram network network.json -o network.png --json
```

## Install

### Marketplace

```bash
claude plugin marketplace add angri450/GroundPA-Toolkit
claude plugin install groundpa-toolkit@angri450
```

Then reload Claude Code:

```text
/reload-plugins
```

### Required .NET Tools

```powershell
dotnet tool install --global Angri450.Nong.Cli
dotnet tool install --global Angri450.Nong.Skill.Manager
```

If the tools are already installed:

```powershell
dotnet tool update --global Angri450.Nong.Cli
dotnet tool update --global Angri450.Nong.Skill.Manager
```

## Workspace

Runtime outputs should go under:

```text
$HOME/Documents/GroundPA Toolkit Workplace/output/
```

Use absolute paths when calling `nong` from agent workflows. For generated files, read the `artifacts` field from JSON output.

## Contract

All Nong-facing skills follow the same rule:

1. Run `nong commands --json` to discover available commands.
2. Use only commands marked `implemented`.
3. Prefer `--json` for model-readable output.
4. Treat `status: "error"` as failure.
5. Read `errors[0].code`, `errors[0].message`, and `artifacts` before taking the next step.

## License

Apache-2.0
