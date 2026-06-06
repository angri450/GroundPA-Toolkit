---
name: icons
description: Bioicons discovery via nong. Trigger on bioicons, scientific icons, lab icons, biology icons, chemistry icons, icon list, or icon search.
---

# Icons

Use `nong icons` for implemented Bioicons discovery.

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

If the .NET host says no compatible framework was found, use Nong 3.2.3+ or set `DOTNET_ROLL_FORWARD=LatestMajor` for the current shell and retry.

## Implemented Commands

```powershell
nong icons list [--json]
nong icons search <query> [--json]
```

## Dispatch

1. To list available scientific icons, run `nong icons list --json`.
2. To find icons by keyword, run `nong icons search <query> --json`.
3. Do not promise icon rendering or SVG export unless the current `nong` output provides the required artifact.
