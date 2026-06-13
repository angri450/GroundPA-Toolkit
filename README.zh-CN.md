# GroundPA Toolkit — 土地公的工具箱

GroundPA Toolkit 2.0.0 是面向农学生论文和文档工作流的 Claude Code skill 集。核心变化是：skill 层不再让模型临时写 .NET 小项目，而是统一调用 `nong` CLI。

模型负责判断任务、准备 JSON/spec、解释诊断结果。确定性工作交给 .NET CLI。

## 基础工具

```powershell
dotnet tool install --global Angri450.Nong.Cli
nong commands --json
```

skill 生命周期工具：

```powershell
dotnet tool install --global Angri450.Nong.Skill.Manager
```

## 2.0.0 暴露的 Nong Skills

只暴露当前 `nong` CLI 已实现的命令。

| Skill | 已实现命令 |
|-------|------------|
| `word` | `word read`, `word preview`, `word fill`, `word rebuild` |
| `inspect` | `inspect diagnose`, `inspect refs`, `inspect write-paper` |
| `excel` | `excel sheets`, `excel read`, `excel to-groups` |
| `chart` | `chart analyze`, `chart anova`, `chart duncan`, `chart bar` |
| `diagram` | `diagram flowchart`, `diagram network` |
| `genre` | `genre list`, `genre show` |
| `icons` | `icons list`, `icons search` |

PPTX 和 OCR 在 2.0.0 不作为 skill 暴露，因为当前 `nong` CLI 里它们还是 stub。

## 其他 Skills

| Skill | 用途 |
|-------|------|
| `bash` | Bash 引用、数组、错误处理、沙箱安全模式 |
| `powershell` | PowerShell cmdlet、模块、错误处理、凭据安全 |
| `dotnet` | C#、MSBuild、ASP.NET Core、EF Core、MAUI、诊断、NuGet |
| `github` | `git` 和 `gh` CLI 工作流 |
| `gitee` | Gitee 与 Gitee MCP 工作流 |
| `ghproxy` | GitHub 链接加速 |
| `nuget` | 包安装、打包、发布 |
| `ilspycmd` | .NET 程序集反编译 |
| `email` | ClawEmail mail-cli 工作流 |
| `skill-manager` | skill 校验、扫描、打包、评测、脚手架 |

## 常用流程

### Word

```powershell
nong word read paper.docx --json
nong word preview paper.docx --json
nong word fill template.docx data.json -o out.docx --json
nong word rebuild dirty.docx -o clean.docx --json
```

### 论文诊断

```powershell
nong inspect diagnose paper.txt --json
nong inspect refs paper.txt --json
nong inspect write-paper spec.json -o paper.docx --json
```

### Excel 到统计图

```powershell
nong excel to-groups data.xlsx --group Treatment --value Yield --raw > groups.json
nong chart analyze groups.json --json
nong chart bar groups.json -o fig.png --json
```

### 图示

```powershell
nong diagram flowchart flow.json -o flow.png --json
nong diagram network network.json -o network.png --json
```

## 安装

### Marketplace

```bash
claude plugin marketplace add angri450/GroundPA-Toolkit
claude plugin install groundpa-toolkit@angri450
```

然后在 Claude Code 里：

```text
/reload-plugins
```

### 必装 .NET 工具

```powershell
dotnet tool install --global Angri450.Nong.Cli
dotnet tool install --global Angri450.Nong.Skill.Manager
```

如果已经安装：

```powershell
dotnet tool update --global Angri450.Nong.Cli
dotnet tool update --global Angri450.Nong.Skill.Manager
```

## 工作区

运行时输出建议统一放在：

```text
$HOME/Documents/GroundPA Toolkit Workplace/output/
```

agent 调用 `nong` 时优先使用绝对路径。生成文件以后读取 JSON 里的 `artifacts` 字段。

## 契约

所有 Nong-facing skills 都遵守同一规则：

1. 先用 `nong commands --json` 看可用命令。
2. 只调用 `implemented` 命令。
3. 面向模型判断时优先加 `--json`。
4. `status: "error"` 一律视为失败。
5. 下一步行动前读取 `errors[0].code`、`errors[0].message` 和 `artifacts`。

## 开源协议

Apache-2.0
