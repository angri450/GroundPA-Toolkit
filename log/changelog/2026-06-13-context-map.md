# Context Map

Date: 2026-06-13

## Changed

- Added `PROJECT_STATE.md` as current truth source.
- Added repository `AGENTS.md`.
- Added stable wiki pages under `docs/wiki/`.
- Added `log/README.md` and `log/plans/README.md`.
- Updated `CLAUDE.md` to point new windows at `PROJECT_STATE.md` first and avoid stale CLI version assumptions.
- Added planner/builder prompt files.
- Clarified that plugin distribution uses `.claude-plugin/` plus `SKILL.md`, not `.claude/skills/*/CLAUDE.md`.

## Verification

- Documentation-only change; no plugin package build required.
- Read-back verification should confirm that new entry files point to `PROJECT_STATE.md` first.
