# GroundPA Toolkit

GroundPA Toolkit 2.2.1 is a Nong CLI-first Claude Code skill set for agricultural paper and document workflows.

The skill layer no longer asks the model to scaffold temporary .NET projects for normal Office work. It routes requests to the deterministic `nong` CLI, then lets the model read JSON output, fix inputs, and compose the next step.

## Design

Model capacity is for semantic work: choosing workflows, drafting specs, interpreting diagnostics, and writing. Deterministic work runs through .NET tools:

```powershell
dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
nong commands --json
```

Nong also provides the primary lifecycle commands for validating, scanning, inventorying, and packaging skills:

```powershell
nong skill inventory . --json
nong skill scan . --json
nong skill package . --json
```

## Implemented Nong Skills

Only implemented `nong` commands are exposed as 2.2.1 skills.

| Skill | Implemented Commands |
|-------|----------------------|
| `word` | check, convert, read, preview, fill, rebuild, extract, dissect, stats, fonts, styles, validate, merge, outline, images, comments, revisions, infer-format, fix-order, protect, embed-font, add paragraph/table/footnote/endnote/image/toc/xref/link/bookmark/comment/math |
| `pdf` | check, dissect, render, images |
| `inspect` | diagnose, refs, write-paper, classify, structure, varplan, evidence, data-req, gap, semantics |
| `excel` | sheets, read, to-groups, create |
| `chart` | analyze, anova, duncan, bar, line, scatter, pie |
| `diagram` | flowchart, network, tree |
| `pptx` | read, slides |
| `multimodal` | ocr check-env, analyze-image, cloud, to-word, models, install-model, local after preflight |
| `genre` | `genre list`, `genre show` |
| `icons` | `icons list`, `icons search` |

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
| `skill-manager` | Meta-skill maintenance, eval references, and legacy skill-manager workflows |

## Common Workflows

### Word

```powershell
nong word check paper.docx --json
nong word dissect paper.docx --output paper.slice --json
nong word fonts paper.docx --json
nong word styles paper.docx --json
nong word preview paper.docx --json
nong word fill template.docx data.json -o out.docx --json
nong word rebuild dirty.docx -o clean.docx --json
nong word add paragraph paper.docx --spec paragraph.json -o out.docx --json
```

For layout, fonts, font sizes, margins, alignment, table borders, or other formatting questions, do not judge from `word read` alone. Start with `word dissect --output`, then inspect `format.json`, `content.jsonl`, and `structure.json` in the slice directory.

### PDF

```powershell
nong pdf check guide.pdf --json
nong pdf dissect guide.pdf --output guide.slice --mode auto --json
nong pdf render guide.pdf --output guide.pages --dpi 150 --json
nong pdf images guide.pdf --output guide.assets --json
```

Use `content.nongmark` as the primary AI-readable PDF artifact. `preview/content.md` is a lossy preview. Local `pdf dissect --mode auto` is the default for selectable-text PDFs; scan-layout reconstruction, tables, page-faithful Word output, and cross-page image stitching need cloud OCR/to-word when a token is available.

### Paper Inspection

```powershell
nong inspect diagnose paper.txt --json
nong inspect refs paper.txt --json
nong inspect write-paper spec.json -o paper.docx --json
nong inspect classify paper.txt --json
nong inspect evidence paper.txt --json
```

### Excel to Statistics to Chart

```powershell
nong excel to-groups data.xlsx --group Treatment --value Yield --raw > groups.json
nong excel create workbook.json -o workbook.xlsx --json
nong chart analyze groups.json --json
nong chart bar groups.json -o fig.png --json
nong chart line line.json -o line.png --json
nong chart scatter scatter.json -o scatter.png --json
nong chart pie pie.json -o pie.png --json
```

### Diagrams

```powershell
nong diagram flowchart flow.json -o flow.png --json
nong diagram network network.json -o network.png --json
nong diagram tree tree.nwk -o tree.png --json
```

### PPTX

```powershell
nong pptx read deck.pptx --json
nong pptx slides deck.pptx --json
```

### OCR and Image QA

```powershell
nong ocr check-env --json
nong ocr install-model pp-ocrv5-mobile --source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json --json
nong ocr analyze-image fig.png -o fig.analysis --json
nong ocr cloud scan.png -o ocr-out --json
nong ocr to-word scan.png -o out.docx --json
```

`ocr cloud` and `ocr to-word` require `PADDLEOCR_ACCESS_TOKEN` from `https://aistudio.baidu.com/account/accessToken`. `ocr analyze-image` checks image structure and layout; it does not recognize text. `ocr local` is implemented through Nong's pure .NET PP-OCRv5 runtime. Install the current-platform first-party `Angri450.Nong.OcrRuntime.*` bundle with the Huawei NuGet source, then treat local OCR as stable only after `localDotNetPpOcrV5.status=ok` and a real image smoke test pass. Runtime package versions track the CLI version; right after a NuGet release, domestic mirrors may lag. Local OCR is single-image text OCR only; use cloud OCR/to-word for PDF, page alignment, tables, Word output, cross-page image stitching, and `nongmark/v1`/Word slice alignment.

## Install

### Skills install (recommended, no Git login, no SSH key)

This is the **classic Claude Code skills installation**: git clone the repo and copy to `~/.claude/skills/`. It does not use the plugin marketplace mechanism, avoids background clone, and won't trigger interactive Gitee login prompts.

**GitCode (recommended, anonymous clone)**

```bash
git clone https://gitcode.com/angri450/GroundPA-Toolkit.git /tmp/groundpa && mkdir -p ~/.claude/skills && cp -r /tmp/groundpa/. ~/.claude/skills/ && rm -rf /tmp/groundpa && dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

**Gitee**

```bash
git clone https://gitee.com/angri450/GroundPA-Toolkit.git /tmp/groundpa && mkdir -p ~/.claude/skills && cp -r /tmp/groundpa/. ~/.claude/skills/ && rm -rf /tmp/groundpa && dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

**GitHub**

```bash
git clone https://github.com/angri450/GroundPA-Toolkit.git /tmp/groundpa && mkdir -p ~/.claude/skills && cp -r /tmp/groundpa/. ~/.claude/skills/ && rm -rf /tmp/groundpa && dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

After install, run `/reload-plugins` or restart Claude Code.

### Plugin Marketplace install (experimental)

This uses the Claude Code plugin marketplace mechanism. It requires Claude Code to clone the repository in the background; Gitee HTTPS may trigger authentication prompts and fail.

Plugin Marketplace installs the skills only. It does not install the required `nong` CLI; install or update the .NET tool below after plugin installation.

```bash
claude plugin marketplace add https://gitcode.com/angri450/GroundPA-Toolkit.git
claude plugin install groundpa-toolkit@angri450
/reload-plugins
```

GitHub alternative:

```bash
claude plugin marketplace add angri450/GroundPA-Toolkit
claude plugin install groundpa-toolkit@angri450
/reload-plugins
```

### Required .NET Tools

```powershell
dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

If already installed:

```powershell
dotnet tool update --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

Nong 3.2.4+ includes `RollForward=LatestMajor` for machines with a newer .NET runtime, pure .NET local OCR runtime deployment, and local PDF slicing. If an older tool build reports "no compatible framework", update the tool or set `DOTNET_ROLL_FORWARD=LatestMajor` for the current shell and retry.

For local OCR, run once after installing the CLI:

```powershell
nong ocr install-model pp-ocrv5-mobile --source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json --json
```

## Update

Skills install: re-clone and overwrite.

```bash
git clone https://gitcode.com/angri450/GroundPA-Toolkit.git /tmp/groundpa && cp -r /tmp/groundpa/. ~/.claude/skills/ && rm -rf /tmp/groundpa
```

Plugin Marketplace:

```bash
claude plugin marketplace update angri450
claude plugin update groundpa-toolkit@angri450
/reload-plugins
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
