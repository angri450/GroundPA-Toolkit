# Nong.Toolkit.Net Architecture

Last updated: 2026-06-13

Nong.Toolkit.Net is a Claude Code multi-plugin marketplace that routes agent work to Nong.Cli.Net.

## Dependency Direction

```text
Nong.Cli.Net
  deterministic commands and JSON contracts
        |
        v
Nong.Toolkit.Net
  skill routing, examples, references, evals, plugin manifests
```

Toolkit should not own deterministic document behavior. If a capability is missing, implement it in `Nong.Cli.Net` first or mark it as unavailable.

## Repository Shape

```text
.claude-plugin/
word/
pdf/
literature/
inspect/
excel/
chart/
diagram/
pptx/
ocr/
genre/
icons/
slice/
skill-grader/
skill-breeder/
skill-tester/
skill-pruner/
references/
log/
```

Each skill can be installed individually. The root plugin installs the bundle.

## Skill Rules

- CLI-mirror skills should match real Nong command groups.
- `SKILL.md` should stay concise.
- Detailed command usage belongs in `references/`.
- Examples and evals should reflect command flags that actually exist.
- Shared CLI prerequisites belong in `references/shared/nong-cli-preflight.md`.
- Do not put `CLAUDE.md` under `.claude/skills/` or any installable skill directory. Repo development instructions and plugin runtime instructions are separate surfaces.
- Use `.claude-plugin/` manifests plus `SKILL.md` for plugin distribution. Root `CLAUDE.md` / `AGENTS.md` are development-only.
