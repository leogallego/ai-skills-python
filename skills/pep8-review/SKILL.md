---
name: pep8-review
description: >-
  Use for a full Python PEP 8 compliance review of a file or directory. Orchestrates all 9 PEP 8 skills (code-layout, imports, string-quotes, whitespace, trailing-commas, comments, naming, programming, type-annotations) to produce a consolidated report. Invoke when user asks to "review Python code", "PEP 8 audit", "check PEP 8 compliance", "lint for PEP 8", or "full PEP 8 review".
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path, glob pattern, or directory]"
  claude-user-invocable: "true"
---

# PEP 8 Full Compliance Review

This meta-skill orchestrates all 9 PEP 8 topic skills to perform a comprehensive compliance review against the target code. Each topic skill covers a distinct section of PEP 8, and this skill runs them all in sequence, collects their findings, deduplicates overlapping issues, and presents a single consolidated report.

The 9 topic skills are:

| Skill | PEP 8 Section |
|---|---|
| `pep8-code-layout` | Indentation, line length, blank lines, source encoding |
| `pep8-imports` | Import ordering, grouping, wildcards, dunder placement |
| `pep8-string-quotes` | Quote consistency, triple-quote conventions |
| `pep8-whitespace` | Whitespace in expressions, around operators, trailing whitespace |
| `pep8-trailing-commas` | Singleton tuples, multi-line collection trailing commas |
| `pep8-comments` | Block comments, inline comments, docstrings |
| `pep8-naming` | All naming conventions (modules, classes, functions, variables, constants) |
| `pep8-programming` | Programming idioms, comparisons, exceptions, return statements |
| `pep8-type-annotations` | Annotation spacing, PEP 484/526 formatting |

## When to Use

- **Full PEP 8 compliance audit** of a file, set of files, or directory
- **Pre-commit or pre-PR review** for PEP 8 compliance
- **Onboarding** -- assessing an existing codebase's PEP 8 adherence

## Review Process

Follow these steps in order:

1. **Identify target files.** Accept a file path, glob pattern, or directory from the argument. If no argument is provided, ask the user. For directories, find all `.py` files recursively.

2. **Check project style configuration.** Look for existing style settings in:
   - `pyproject.toml` (sections `[tool.ruff]`, `[tool.flake8]`, `[tool.black]`, `[tool.isort]`)
   - `.editorconfig`
   - `setup.cfg` (section `[flake8]`)
   - `ruff.toml` / `.ruff.toml`
   - `tox.ini` (section `[flake8]`)

   Note any deviations from PEP 8 defaults (e.g., max line length set to 120). These deviations affect how findings are classified -- see Pragmatic Consistency below.

3. **Run each topic skill in sequence** against the target files:
   1. `pep8-code-layout` -- indentation, line length, blank lines, encoding
   2. `pep8-imports` -- ordering, grouping, wildcards, dunder placement
   3. `pep8-string-quotes` -- quote consistency, triple quotes
   4. `pep8-whitespace` -- expression spacing, operators, trailing whitespace
   5. `pep8-trailing-commas` -- singleton tuples, multi-line collections
   6. `pep8-comments` -- block/inline comments, docstrings
   7. `pep8-naming` -- all naming conventions
   8. `pep8-programming` -- idioms, comparisons, exceptions, returns
   9. `pep8-type-annotations` -- annotation spacing, PEP 484/526

4. **Collect and deduplicate findings.** If multiple skills flag the same line for related reasons, consolidate into a single finding referencing the most specific skill.

5. **Present consolidated report.** Use the report format below.

## Report Format

Structure the final report as follows:

```
## PEP 8 Review: {filename or directory}

### Project Configuration
- [List any detected style config and deviations from PEP 8 defaults]

### Errors (must fix)
- file.py:12 -- [description of violation]

### Warnings (should fix)
- file.py:45 -- [description of violation]

### Suggestions (consider)
- file.py:78 -- [description of suggestion]

### Summary
- Errors: N
- Warnings: N
- Suggestions: N
- Files reviewed: N
```

Classify findings into these three severity levels:

- **Errors** -- clear PEP 8 violations that will cause confusion or are universally agreed upon (e.g., mixing tabs and spaces, wildcard imports, bare `except:`).
- **Warnings** -- PEP 8 violations that are broadly accepted but where project style may legitimately differ (e.g., line length between 79 and 99).
- **Suggestions** -- stylistic improvements where PEP 8 expresses a preference but acknowledges alternatives (e.g., single vs double quotes when both are acceptable).

## Pragmatic Consistency

PEP 8 itself states: "A foolish consistency is the hobgoblin of little minds." Apply this principle during the review:

- If the project has a style configuration that deviates from PEP 8 defaults (e.g., `max-line-length = 120` in `pyproject.toml`), **respect the project setting**. Do not flag lines between 80 and 120 as errors when the project has deliberately chosen 120.
- Follow PEP 8's consistency hierarchy: **Style Guide < Project conventions < Module conventions**. A project-wide decision always overrides the general PEP 8 recommendation.
- Flag only **genuine inconsistencies**, not deliberate project-level choices. For example, if every file in the project uses double quotes and the project config enforces double quotes, do not suggest switching to single quotes.
- When a project convention conflicts with PEP 8, note the deviation in the Project Configuration section but do not classify it as an error.

## What This Review Does NOT Cover

- **PEP 257** -- docstring content conventions beyond basic format checks (e.g., imperative mood, summary line placement). The `pep8-comments` skill covers docstring formatting as described in PEP 8 only.
- **Type system correctness** -- only annotation formatting and spacing are checked, not whether types are logically correct or complete.
- **Code logic, performance, or security** -- this is a style review, not a code review.
- **Tool-specific rules** -- ruff, flake8, or pylint rules that go beyond what PEP 8 specifies are out of scope.
