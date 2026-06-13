# 开发说明

## 当前状态

- 仓库：`https://github.com/angri450/GroundPA-Toolkit`
- 本地：`<repo-root>`
- 版本：v2.0.0
- 架构：Nong CLI-first skill layer

## 2.0.0 边界

只把当前 `nong commands --json` 中标记为 `implemented` 的能力做成 skill。

已暴露：

```text
word, inspect, excel, chart, diagram, genre, icons
bash, powershell, dotnet, github, gitee, ghproxy, nuget, ilspycmd, email, skill-manager
```

暂不暴露：

```text
pptx, multimodal/ocr
```

原因：当前 `nong` CLI 中 PPTX 和 OCR 仍是 stub。等真实命令实现并验证后，再恢复对应 `SKILL.md` 并加入 `.claude-plugin/plugin.json`。

## 日常提交流程

```bash
git status
git add <file>
git commit -m "类型: 简述"
git push origin master:main
```

## Commit 信息格式

| 类型 | 用途 |
|------|------|
| `feat:` | 新功能、新 skill |
| `fix:` | 修 bug |
| `docs:` | 文档改动 |
| `refactor:` | 重构，不改功能 |
| `chore:` | 杂务、配置、版本号 |

## 发布新版本

1. 确认 `git status`。
2. 修改 `.claude-plugin/plugin.json` 和 `skills.sh.json` 的版本号。
3. 确认 `README.md` / `README.zh-CN.md` 的安装说明同步。
4. 本地校验插件。
5. 提交、推送、打 tag。

```bash
git tag -a vX.Y.Z -m "GroundPA Toolkit vX.Y.Z"
git push origin vX.Y.Z
```

## 注意事项

1. 不要把未实现的 `nong` stub 写进 skill description。
2. 不要让 skill 默认生成临时 .NET 项目调用旧 NuGet 包。
3. Nong-facing skill 优先使用 `--json`。
4. 生成物路径以 JSON 的 `artifacts` 字段为准。
5. 不要提交 `bin/`、`obj/`、`.zip`、`.nupkg`。
