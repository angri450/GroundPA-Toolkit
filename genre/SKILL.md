---
name: genre
description: >
  Academic paper writing and analysis via Nong.Genre NuGet package. Trigger on
  paper, thesis, 论文, 诊断, diagnosis, evidence chain, 证据链, reference check,
  参考文献, variable plan, 变量方案, GB/T 7714, 公文, official document, 信件, letter.
---

# Genre — Academic Paper Intelligence & Writing

Two capabilities, loaded on demand:

- **Paper Analysis** → load [paper-analysis.md](references/paper-analysis.md)
- **Write Paper** → load [write-paper.md](references/write-paper.md)

## Dependencies

- .NET SDK 8.0+ (`dotnet` command available)
- NuGet packages: `Angri450.Nong.Docx` + `Angri450.Nong.Genre`

Dependency chain: ThirdParty → Docx → Genre.

If .NET SDK 8.0+ is missing, stop immediately and tell the user to install it. Do not attempt to fix.

## Dispatch Logic

1. User mentions "分析", "诊断", "分类", "提取", "检查", "analyze", "diagnose", "classify", "extract" → **load paper-analysis.md**
2. User mentions "生成", "写", "创建", "论文", "generate", "write", "create", "paper" → **load write-paper.md**
3. User mentions "公文", "official document" → reply "公文写作功能开发中，暂不可用。"
4. User mentions "信件", "信函", "letter" → reply "信函写作功能开发中，暂不可用。"
5. Both analysis + writing → analysis first, then writing

## Workspace

First use: create the .NET project with three commands:

```powershell
dotnet new console -n GenreWriter -o <target-dir> --force
dotnet add <target-dir> package Angri450.Nong.Docx
dotnet add <target-dir> package Angri450.Nong.Genre
```

Then write a `Program.cs` template. See [workspace-setup.md](references/workspace-setup.md) for the full template and details.

After setup, each session only modifies `Program.cs`.

## Subcommand Quick Reference

```
dotnet run --project <path> -- classify <text>       → PaperTypeClassifier
dotnet run --project <path> -- structure <text>      → PaperStructureExtractor
dotnet run --project <path> -- diagnose <docx>       → PaperDiagnostics (full pipeline)
dotnet run --project <path> -- references <text>     → ReferenceAnalyzer
dotnet run --project <path> -- variable-plan <text>  → VariablePlanGenerator
dotnet run --project <path> -- analyze <docx>        → Full diagnosis pipeline
```

## Key Conventions

- Paper writing uses `PaperWriter`, engine operations use `DocumentWriter`
- GB/T 7714 style via `Gbt7714Style`, NOT `StyleBuilder.BuildAll()`
- Do NOT reference `Angri450.Nong.Docx` paper methods — those are removed from `DocumentWriter`
- 公文 (official document) and 信件 (letter) are coming soon — do not pretend they are implemented
