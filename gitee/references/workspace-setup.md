# Gitee Skill — Workspace Setup

## 1. Get Personal Access Token

Visit https://gitee.com/profile/personal_access_tokens to generate a token.

Required scopes: `projects`, `issues`, `pull_requests`, `notifications`

## 2. Configure MCP Server

Add to `~/.claude/mcp.json`:

```json
{
  "mcpServers": {
    "gitee": {
      "url": "https://api.gitee.com/mcp",
      "headers": {
        "Authorization": "Bearer <your-personal-access-token>"
      }
    }
  }
}
```

## 3. Verify

Restart Claude Code, run `/mcp`, confirm `gitee` appears in connected servers.

## 4. Usage

Once configured, describe your intent directly. The skill activates based on keywords:

```
Create a bug issue in owner/repo
```

```
Review PR #42 in owner/repo
```

```
Merge feature/login into main, create a PR
```

```
What's pending for me today?
```

```
Search gitee for C# chart libraries
```
