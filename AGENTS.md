# Nong.Toolkit.Net Agent Instructions

This file governs the repository.

## Required Entry

- Read `PROJECT_STATE.md` first. It is the current truth source.
- Then read `CLAUDE.md` for repository workflow and plugin details.
- Read only the active plan linked from `PROJECT_STATE.md`, unless the user asks for historical research.
- Treat `log/` as historical archive.

## Scope

- This repository owns Claude Code skills and plugin manifests for agricultural/document workflows.
- Deterministic Word/PDF/OCR/Excel/PPTX/chart/diagram/literature behavior belongs in `Nong.Cli.Net`.
- Toolkit skills must route to real, verified command surfaces. Do not advertise commands or flags that do not exist.

## Required Loop

1. Confirm current state through `PROJECT_STATE.md`.
2. For substantial work, write or update a plan under `log/plans/` and update `PROJECT_STATE.md` if another window will execute it.
3. Read the relevant `SKILL.md`, references, examples, and evals before editing.
4. Verify changed skills with `nong skill validate` and repository-level inventory/scan/package checks when appropriate.
5. Record changes under `log/changelog/` and update indexes.

## Safety

- Do not publish or push without explicit user request.
- Do not commit generated package zips, local cache, or archived experiments.
- Do not revert unrelated user changes.
