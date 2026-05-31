# 开发说明

## 当前状态

- 仓库：`https://github.com/angri450/GroundPA-Toolkit`
- 本地：`C:\Users\Administrator\Documents\Github\GroundPA Toolkit`
- 分支：`master`（本地）→ `main`（远程）
- 版本：v1.0.0

## 日常提交流程

```bash
# 1. 看改了啥
git status

# 2. 加文件
git add <file>           # 加指定文件
git add -A               # 加所有改动

# 3. 提交
git commit -m "类型: 简述"

# 4. 推送
git push origin master:main
```

## Commit 信息格式

遵循 Conventional Commits，类型用以下之一：

| 类型 | 用途 |
|------|------|
| `feat:` | 新功能、新 skill |
| `fix:` | 修 bug |
| `docs:` | 文档改动 |
| `refactor:` | 重构，不改功能 |
| `chore:` | 杂务（配置、路径等） |

示例：
```
feat: add xxx skill
fix: correct output path in word workspace
docs: update README skill table
```

## 发布新版本

```bash
# 1. 确认所有改动已提交
git status

# 2. 改 plugin.json 版本号
# .claude-plugin/plugin.json → "version": "x.y.z"

# 3. 提交版本号变更
git add .claude-plugin/plugin.json
git commit -m "chore: bump version to x.y.z"
git push origin master:main

# 4. 打 tag
git tag -a vX.Y.Z -m "GroundPA Toolkit vX.Y.Z — 简述"
git push origin vX.Y.Z

# 5. 创建 GitHub Release
gh release create vX.Y.Z \
  --repo angri450/GroundPA-Toolkit \
  --title "GroundPA Toolkit vX.Y.Z" \
  --notes "改动说明..."
```

## 注意事项

1. 不要用 `--force` 推送，除非确有必要
2. 推送前用 `git log --oneline` 确认提交信息无敏感内容
3. 不要提交 `bin/`、`obj/`、`.zip`、`.nupkg`（已在 .gitignore 排除）
4. 发布前跑一遍审计，确认无硬编码路径、无内部项目名

## 推广给朋友

### 方式一：发一键安装命令

发给朋友复制粘贴即可：

```bash
git clone https://github.com/angri450/GroundPA-Toolkit.git /tmp/groundpa && cp -r /tmp/groundpa/* ~/.claude/skills/ && rm -rf /tmp/groundpa && dotnet tool install --global Angri450.Nong.Skill.Manager
```

Windows 用户把 `cp -r ...` 换成 `Copy-Item /tmp/groundpa/* -Destination ~/.claude/skills/ -Recurse`。

前提：朋友需要装 [.NET SDK](https://dotnet.microsoft.com/download) 和 [Git](https://git-scm.com/)。

### 方式二：Claude Code Marketplace（推荐）

本仓库本身就是 marketplace，朋友只需两条命令：

```bash
claude plugin marketplace add angri450/GroundPA-Toolkit
claude plugin install groundpa-toolkit@angri450
```

然后在 Claude Code 中 `/reload-plugins`，即可通过命名空间调用：

```text
/groundpa-toolkit:word
/groundpa-toolkit:chart
```

注意：是 `plugin`（单数），不是 `plugins`。安装格式是 `plugin-name@marketplace-name`。

**提交到公共目录：**

1. 先本地校验：`claude plugin validate .`
2. Anthropic Community Marketplace：通过 claude.ai 上的表单提交，审核通过后用户可通过 `claude-community` 市场安装
3. `kossakovsky/cc-plugins`：第三方社区市场，可 PR 提交

### 方式三：直接分享仓库链接

```
https://github.com/angri450/GroundPA-Toolkit
```

仓库首页有完整 README，朋友自行按说明安装。
