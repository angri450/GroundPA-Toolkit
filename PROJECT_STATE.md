# Nong.Toolkit.Net Project State

Last updated: 2026-06-13

This file is the current truth source for agents. Read it before `CLAUDE.md`, `AGENTS.md`, `README.md`, or any file under `log/`.

## Current Work

Active plan/handoff:

- None.

There is no active construction plan. If work is needed, the planner window must create or update a plan under `log/plans/`, update `log/plans/index.md`, and then update this section before a builder window starts.

Do not infer current work from older `log/plans/*` files.

## Current Role

Nong.Toolkit.Net is the Claude Code agricultural/document skill marketplace for Nong.Cli.Net.

It teaches agents how to route Word, PDF, Excel, PPTX, OCR, chart, diagram, literature, inspect, slice, and skill lifecycle work through the deterministic `nong` CLI.

This repository should not implement deterministic document logic. That belongs in `Nong.Cli.Net`.

## Current Plugin Surface

Current line:

- marketplace id: `nong-toolkit`
- root plugin id: `nong-toolkit`
- current release line: `1.0.0` after decoupling Toolkit version from CLI version
- install shape: one full bundle plus individual low-token plugins

Current skill/plugin areas:

- `word`
- `pdf`
- `literature`
- `inspect`
- `excel`
- `chart`
- `diagram`
- `pptx`
- `ocr`
- `genre`
- `icons`
- `slice`
- `skill-grader`
- `skill-breeder`
- `skill-tester`
- `skill-pruner`

## Nong CLI Contract

Toolkit targets the current installed or locally built `nong` CLI.

Do not hardcode a CLI package version in skill logic. Verify the command surface with:

```powershell
nong commands --json
nong commands --format openai-tools
```

If the global tool is stale, use a current local build from sibling repo `..\Nong.Cli.Net`.

Latest known sibling state on 2026-06-13:

- `Nong.Cli.Net` command discovery: `126 commands available`
- OpenAI tool schemas: `126`
- CLI and Toolkit versions are intentionally decoupled

If this file disagrees with live `nong commands --json`, treat that as current-state drift and investigate before changing skills.

## Planning Workflow

Development plans live in `log/plans/`.

Only the plan linked above is active for a builder window. Older plans remain as history and must not be scanned to infer current work.

Two-window workflow:

- Planner window: reads history as needed, writes or updates `log/plans/YYYY-MM-DD-topic.md`, updates `log/plans/index.md`, then updates this file's active plan pointer.
- Builder window: reads this file and the active plan only, then implements and verifies.

Detailed policy:

- `docs/wiki/planning-workflow.md`
- `log/plans/README.md`

## Current Architecture

The repository is a Claude Code multi-plugin marketplace:

```text
.claude-plugin/
  marketplace.json
  plugin.json
<skill>/
  SKILL.md
  .claude-plugin/plugin.json
  references/
  examples/
  evals/
references/
log/
```

Each CLI-facing skill should mirror a real Nong command group or a clearly defined lifecycle role.

Plugin distribution boundary:

- installable skill behavior lives in each skill directory's `SKILL.md`, `references/`, examples, evals, and `.claude-plugin/plugin.json`;
- repository-level `CLAUDE.md` and `AGENTS.md` are development instructions for this repo, not the plugin runtime payload;
- do not create or publish `.claude/skills/*/CLAUDE.md` for these skills, because that collides with Claude Code's project/global instruction model;
- `.claude/` is ignored here; use `.claude-plugin/` for marketplace manifests.

## Current Risks

- Older docs and logs mention CLI version `4.0.0`, `4.1.0`, `4.1.1`, or command count `125`; those are historical unless confirmed by current `nong commands --json`.
- Toolkit can drift if Nong.Cli.Net adds, renames, or moves commands without a skill sync.
- Accidentally mixing repo development instructions into the plugin skill surface will create confusing Claude Code context conflicts.
- Generated package zips and archived experiments belong outside the repository, usually under `../Nong.Toolkit_archive/`.

## Information Sources

Use this order:

1. `PROJECT_STATE.md` for current truth.
2. `AGENTS.md` and `CLAUDE.md` for agent behavior.
3. The active plan linked above, if any.
4. `docs/wiki/` for stable project knowledge.
5. `log/` only as historical evidence.

Never bulk-read `log/` to decide current work.

## Verification Baseline

Useful validation commands:

```powershell
claude plugin validate .
nong skill inventory . --json
nong skill scan . --json
nong skill package . --json
```

For changed skills:

```powershell
nong skill validate .\<skill-name> --json
```
