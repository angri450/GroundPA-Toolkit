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
