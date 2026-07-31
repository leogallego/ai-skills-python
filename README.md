# ai-skills-python

Portable **Python agent skills** for writing, reviewing, and hardening Python code.

- **Format:** [agentskills.io](https://agentskills.io/specification) (`skills/<name>/SKILL.md`)
- **Distribution:** [Lola](https://github.com/LobsterTrap/lola) module (install to Cursor, Claude Code, Copilot, Gemini, OpenCode) *or* native Cursor Agent Skills symlinks
- **Collections:** `pep8` (style) and `quality` (types, tests, try/except, design review, …)

This repo merges and adapts:

| Source | What came from it |
|--------|-------------------|
| [`claude-skills-python-pep8`](../claude-skills-python-pep8) | 10 PEP 8 topic skills + `pep8-review` |
| [`claude-skills-python`](../claude-skills-python) (Honnibal) | Quality skills (`try-except`, `tighten-types`, …) |

See [AGENTS.md](AGENTS.md) for migration status and work remaining for contributors/agents.

---

## Layout (Lola module + agentskills.io)

```text
ai-skills-python/
├── AGENTS.md                 # Handoff / remaining work for agents
├── README.md
├── LICENSE                   # MIT
├── NOTICE                    # Dual attribution
├── .claude-plugin/plugin.json
├── scripts/install-cursor.sh # Symlink skills into ~/.cursor/skills/
└── skills/
    ├── pep8-review/SKILL.md
    ├── pep8-naming/SKILL.md
    ├── …
    ├── try-except/SKILL.md
    ├── tighten-types/SKILL.md
    └── stub-package/
        ├── SKILL.md
        └── scripts/stub_package.py
```

Each skill directory name **must** match the `name:` field in `SKILL.md` (agentskills.io rule).

Claude-only frontmatter (`argument-hint`, `user-invocable`, `disable-model-invocation`) lives under `metadata.claude-*` so the top-level YAML stays spec-valid.

---

## Install

### Option A — Lola (multi-assistant)

```bash
uv tool install lola-ai

# From a local checkout
lola mod add /home/lgallego/Claude/ai-skills-python
lola install ai-skills-python --scope user
# or project scope:
# lola install ai-skills-python
```

Optional: publish later via [lola-market](https://github.com/RedHatProductSecurity/lola-market).

Docs: https://lobstertrap.org/lola/

**Note:** On Cursor, Lola may install as `.cursor/rules/*.mdc` (and keep sources under `.lola/modules/`), which is not the same as native Agent Skills under `~/.cursor/skills/`. Use Option B if you want Cursor’s skill loader.

### Option B — Native Cursor Agent Skills

```bash
./scripts/install-cursor.sh
# or manually:
# mkdir -p ~/.cursor/skills
# for d in "$PWD"/skills/*/; do
#   ln -sfn "$d" ~/.cursor/skills/"$(basename "$d")"
# done
```

Start a new chat so Cursor picks up the skills.

### Option C — Claude Code plugin

```bash
claude plugin add /home/lgallego/Claude/ai-skills-python
```

---

## Validate

```bash
# Spec validator (agentskills / skills-ref package):
for d in skills/*/; do
  uvx --from skills-ref agentskills validate "$d"
done

# Lightweight name↔directory check (no network):
for d in skills/*/; do
  test -f "$d/SKILL.md" || { echo "missing SKILL.md in $d"; exit 1; }
  name=$(basename "$d")
  grep -q "^name: ${name}$" "$d/SKILL.md" || { echo "name mismatch in $d"; exit 1; }
done
echo "OK: name matches directory for all skills"
```

---

## Skill index

### Collection: pep8

| Skill | Role |
|-------|------|
| `pep8-review` | Full PEP 8 audit (orchestrates the rest) |
| `pep8-code-layout` | Indentation, line length, blank lines |
| `pep8-imports` | Import order and style |
| `pep8-string-quotes` | Quote style |
| `pep8-whitespace` | Expression spacing |
| `pep8-trailing-commas` | Trailing commas |
| `pep8-comments` | Comments and docstrings (PEP 8 §7) |
| `pep8-naming` | Naming conventions |
| `pep8-programming` | None compares, exceptions, truthiness, … |
| `pep8-type-annotations` | Annotation style |

### Collection: quality

| Skill | Role |
|-------|------|
| `try-except` | Scope and tighten try/except |
| `tighten-types` | Improve type annotations |
| `contract-docstrings` | Contract-style docstrings |
| `hypothesis-tests` | Property-based tests |
| `mutation-testing` | Mutation testing workflow |
| `pre-mortem` | Pre-mortem fragility analysis |
| `stub-package` | Structural stub overview (`scripts/stub_package.py`) |
| `conceptual-analysis` | Naming / domain concept clarity |
| `alignment-chart` | Alignment / tradeoff charting |

---

## Related

- Spec: https://agentskills.io/specification  
- Lola: https://github.com/LobsterTrap/lola  
- ansible-know review note (local): `~/Claude/ansible-knowledge-mcp/docs/research/codebase-review-2026-07-31.md` (gitignored there)

## License

MIT. See [LICENSE](LICENSE) and [NOTICE](NOTICE).
