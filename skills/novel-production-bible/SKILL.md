---
name: novel-production-bible
description: Create or maintain a project-local production bible for AI short drama, covering visual constraints, identity continuity, spatial logic, H3 delivery, and asset lifecycle without hard-coding them into generic novel skills.
metadata:
  short-description: Project-local AI drama production rules
---

# Novel Production Bible

Use this skill when a drama project needs reusable, project-specific production constraints, or when existing constraints have drifted across art, character, script, and storyboard assets. Do not use it to write a story, generate images, or replace the relevant `novel-*` craft skill.

The production bible belongs in the project root, normally as both `production-bible.json` (machine-readable source) and `production-bible.md` (reviewable brief). It holds decisions that are true for this project only; generic skills must not absorb its palette, genre, character names, or preferred camera language.

## Scope

Keep only constraints that downstream production must obey. Prefer observable rules over taste labels:

- visual production: camera staging, allowed close-ups, image cleanliness, controlled ageing;
- identity continuity: shared-face groups, immutable features, permitted differentiators;
- spatial continuity: access logic, fixed anchors, directional rules, state changes;
- video delivery: reference mode, keyframe locking, dialogue and audio-reference conventions;
- asset lifecycle: source-of-truth files, generated outputs, replacement and Git policy.

Read [the schema guide](references/schema.md) before creating or revising a bible. Use [the review checklist](references/checklist.md) when auditing a project already in production.

## Rules

- Keep the bible project-local. Never copy project-specific content into a reusable skill.
- Do not duplicate upstream story facts. Reference character, scene, and prop IDs already owned by `cast.json`, `art.json`, `script.json`, and `storyboard.json`.
- Write only enforceable constraints. Record a rationale when a rule prevents a known generation or continuity failure.
- Separate universal defaults from project overrides. If a constraint only applies to a location, character form, scene state, or delivery platform, scope it there.
- When a bible changes, identify affected derived assets and regenerate their reports, manifests, and prompt packages; do not silently leave stale references.

## Handoff to other novel skills

When a project-local bible exists, `novel-characters`, `novel-art`, `novel-script`, and `novel-storyboard` should read the sections relevant to their task before producing or revising assets. Their own JSON outputs remain authoritative for their native domain; the bible supplies cross-cutting constraints only.
