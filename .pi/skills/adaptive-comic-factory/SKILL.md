---
name: adaptive-comic-factory
description: Coordinate research, story design, comic rigging, adaptive reading, and QA for reusable comic packages. Use for folktales, fairy tales, myths, biographies, public-domain works, and original stories.
---

# Adaptive Comic Factory

Use this skill when the user wants to turn a story candidate into a validated, adaptive comic package.

The factory is a backend authoring and rigging system. It does not depend on a particular image generator. Store narrative intent, continuity, art constraints, lettering plans, learning supports, and research lineage. Do not store renderer-specific payloads in canonical story files.

## Operating model

One ChatGPT/Codex window acts as the **controller**. Parallel worker windows use DevSpace worktrees. The controller owns shared state and gates; workers own only their assigned output paths.

Read these files before starting:

- `references/workflow.md`
- `references/roles.md`
- `references/data-contract.md`

Use `tools/factory-control.mjs` to create and inspect runs.

## Core rules

1. Research facts once. Derived artifacts reference them by stable IDs.
2. Distinguish the traditional work from a translation, edition, scan, illustration set, recording, or modern retelling.
3. Every consequential research claim needs a source and an evidence anchor.
4. Every adaptation decision cites claims.
5. Every story seed cites decisions.
6. Every canonical non-original entity, event, setting rule, or theme cites a seed.
7. Original additions are labeled `original_contribution`; never disguise them as traditional material.
8. Events are independent of pages and panels.
9. Art remains consistent across reading levels. Adapt language, inference load, scaffolding, assessment, and interaction instead.
10. Generated art contains no final lettering. Reserve composition space for later balloons and captions.
11. The controller may reject a worker submission even when it is syntactically valid.
12. Do not approve legal or cultural conclusions automatically. Record uncertainty and block production when required.

## Controller workflow

1. Initialize a run.
2. Generate task packets from the workflow.
3. Assign independent tasks to worktree windows.
4. Ask each worker to read its task packet and this skill.
5. Review worker commits before integrating them.
6. Reconcile run status.
7. Run validation at every gate.
8. Approve gates only after evidence and QA are complete.

Example:

```bash
node .pi/skills/adaptive-comic-factory/tools/factory-control.mjs init \
  the-firebird --title "The Firebird" --profile traditional_tale

node .pi/skills/adaptive-comic-factory/tools/factory-control.mjs status \
  factory-runs/the-firebird

node .pi/skills/adaptive-comic-factory/tools/factory-control.mjs bundle \
  factory-runs/the-firebird research-source-map \
  --output factory-runs/the-firebird/bundles/research-source-map.json
```

## Worker workflow

A worker must:

1. Read its immutable task packet.
2. Read only the scoped input bundle and necessary sources.
3. Write only to `allowed_write_paths`.
4. Preserve IDs and cite exact evidence anchors.
5. Produce the required output files.
6. Write one submission manifest.
7. Run task-specific checks.
8. Commit with the task packet's commit message.
9. Return the commit SHA and unresolved questions to the controller.

Workers must not edit `run.json`, another worker's output, workflow definitions, or gate records.

## Research profiles

Supported profiles:

- `traditional_tale`
- `public_domain_literary_work`
- `myth_or_religious_narrative`
- `historical_or_biographical`
- `licensed_work`
- `original_story`

Profiles activate different research questions. Do not force folklore-specific work onto an original story or treat a living religious tradition as merely a copyright problem.

## Required release chain

```text
source item
  -> evidence anchor
  -> research claim
  -> adaptation decision
  -> story seed
  -> canonical story element
  -> page/panel presentation
  -> adaptive learning element
  -> QA finding or approval
```

The run cannot pass the research gate when this chain is broken for a consequential element.

## Scope control

Keep the canonical package small:

- one project manifest;
- one research dossier;
- one story bible;
- one semantic event file;
- page rigs;
- adaptive content;
- validation and QA reports.

Renderer adapters, model settings, render histories, and API payloads belong outside the canonical factory contract.
