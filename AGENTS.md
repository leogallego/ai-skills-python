# Agent handoff — ai-skills-python

Read this file when continuing work on this repository.

## Goal

Single public-ready Python skill pack that is:

1. **agentskills.io compliant** — `skills/<name>/SKILL.md`, `name` matches directory, valid frontmatter
2. **Lola-installable** — repo root is a Lola module (`skills/` auto-discovery; optional `commands/`, `agents/`)
3. **Cursor-friendly** — also installable via symlinks into `~/.cursor/skills/`

Not a second skill format for Lola. Author to the spec; Lola distributes.

## Sources (do not delete yet)

| Path | Role |
|------|------|
| `~/Claude/claude-skills-python-pep8/` | Original PEP 8 plugin (Leonardo Gallego, MIT) |
| `~/Claude/claude-skills-python/` | Original quality skills (Matthew Honnibal, MIT) + `stub_package.py` |

Migrated content lives under `skills/` in this repo.

## Done

- [x] Create repo at `~/Claude/ai-skills-python` (`git init -b main`, no commits yet)
- [x] Copy 10 pep8 skills → `skills/pep8-*/SKILL.md`
- [x] Convert 9 quality flat `.md` skills → `skills/<name>/SKILL.md`
- [x] Move Claude-only keys into `metadata.claude-*`
- [x] Add top-level `license` + `compatibility` (agentskills optional fields)
- [x] Copy `stub_package.py` → `skills/stub-package/scripts/`
- [x] `README.md` (install: Lola / Cursor / Claude; index; layout)
- [x] This `AGENTS.md`
- [x] Fix `stub-package` paths → `./scripts/stub_package.py`
- [x] `NOTICE` (dual MIT attribution)
- [x] `.claude-plugin/plugin.json`
- [x] Normalize quality `metadata.author` / `adapted-by`
- [x] Name↔directory validation for all 19 skills

## Remaining (do next)

- [x] Run `uvx --from skills-ref agentskills validate` on each skill (all 19 OK)
- [x] `scripts/install-cursor.sh` symlink helper
- [x] Symlink into `~/.cursor/skills/` via `scripts/install-cursor.sh` (19 skills)
- [x] Initial git commit + GitHub remote (`leogallego/ai-skills-python`)
- [ ] Later: archive/redirect old source repos, optional lola-market entry
- [ ] After remote is solid: replace local old checkouts and verify install paths

## Frontmatter contract

```yaml
---
name: try-except                    # must == parent directory
description: >-                     # what + when, ≤1024 chars
  …
license: MIT
compatibility: Agentskills.io clients (…). Optional Lola install.
metadata:
  author: …
  version: "1.0.0"
  collection: pep8 | quality
  claude-argument-hint: "…"         # optional Claude UX
  claude-user-invocable: "true"    # optional
  claude-disable-model-invocation: "true"  # optional
---
```

Do **not** put `argument-hint`, `user-invocable`, or `disable-model-invocation` at the top level (Claude-only; may fail `skills-ref`).

## Related context (ansible-know)

Codebase review that led to this pack:

`~/Claude/ansible-knowledge-mcp/docs/research/codebase-review-2026-07-31.md`

(that path is gitignored in ansible-know; file exists on disk.)

## How to use these skills while hacking ansible-know

1. Finish remaining checklist above
2. Symlink into `~/.cursor/skills/` (README Option B) **or** `lola install`
3. New Cursor chat — skills should appear for Python / PEP 8 work (e.g. issue #190)

## Out of scope

- Generating Ansible module skills (that stays in ansible-know-mcp)
- Shipping FastMCP 4 migration here
- Inventing a Lola-specific skill format
