---
name: diagram
description: Scientific diagram CLI via nong. Trigger on flowchart, workflow diagram, process diagram, network graph, relationship graph, or mechanism network.
---

# Diagram

Use `nong` for implemented scientific diagram rendering. Do not claim tree/Newick rendering in 2.0.0.

## Prerequisites

Run once before work:

```powershell
nong commands --json
```

If `nong` is missing, tell the user to install:

```powershell
dotnet tool install --global Angri450.Nong.Cli
```

## Implemented Commands

```powershell
nong diagram flowchart <spec.json> -o <out.png> [--json]
nong diagram network <spec.json> -o <out.png> [--json]
```

## Dispatch

1. For process, workflow, protocol, or pipeline diagrams, prepare a flowchart JSON spec and run `nong diagram flowchart`.
2. For relationship, mechanism, interaction, or network diagrams, prepare a network JSON spec and run `nong diagram network`.
3. Read `artifacts.png` from JSON output for the generated image path.
4. If the user asks for phylogenetic trees or Newick, say it is not implemented in the current `nong` CLI.
5. For Bioicons listing/search, use the `icons` skill.

## Contract

Always use `--json` for generated diagrams. Treat `status: "error"` as failed and fix the spec before retrying.
