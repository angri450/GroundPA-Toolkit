---
name: genre
description: Template discovery via nong. Trigger on available writing templates, genre templates, format presets, template list, or showing a template.
---

# Genre

Use `nong genre` only for template discovery. Paper diagnosis and paper generation live in the `inspect` skill.

## Nong CLI Preflight

Claude Plugin Marketplace installs the skills, not the `nong` CLI. Run once before work:

```powershell
nong commands --json
```

If `nong` is missing, install or update:

```powershell
dotnet tool install --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
dotnet tool update --global Angri450.Nong.Cli --add-source https://mirrors.huaweicloud.com/repository/nuget/v3/index.json
```

If the .NET host says no compatible framework was found, use Nong 3.2.4+ or set `DOTNET_ROLL_FORWARD=LatestMajor` for the current shell and retry.

## Implemented Commands

```powershell
nong genre list [--json]
nong genre show <name> [--json]
```

## Dispatch

1. To see available templates, run `nong genre list --json`.
2. To inspect one template, run `nong genre show <name> --json`.
3. Do not claim official document writing, letter writing, or full paper writing from this skill.
4. For writing a paper from a JSON spec, use `nong inspect write-paper`.
