---
name: pep8-comments
description: >-
  Use when writing or reviewing Python comments and docstrings — block comments, inline comments, documentation strings, comment style. Invoke when user mentions "comments", "docstrings", "documentation", "PEP 8 comments", or asks about Python comment conventions.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Comments (Section 7)

Apply the following rules when writing, reviewing, or modifying Python comments and docstrings. These rules come directly from PEP 8.

---

## General Principles

- Comments that contradict the code are worse than no comments. Always prioritize keeping comments accurate over having more comments.
- Keep comments up to date when code changes. Stale comments mislead future readers.
- Comments should be complete sentences. End them with a period.
- The first word of a comment should be capitalized, unless it is a lowercase identifier that must not be altered (never change the case of identifiers).
- English is the preferred language for comments, unless you are 120% certain that the code will never be read by someone who does not speak your language.

---

## 7.1 Block Comments

Block comments apply to the code that follows them and are indented to the same level as that code. Each line of a block comment starts with `# ` (a hash followed by a single space). Separate paragraphs inside a block comment with a line containing only `#`.

### Correct

```python
# This is a block comment that applies
# to the code below.
#
# This is a second paragraph in the
# same block comment.
def process_data():
    pass
```

### Wrong -- wrong indentation

```python
    # This comment is indented too far
    # relative to the code it describes.
def process_data():
    pass
```

### Wrong -- missing space after hash

```python
#This block comment is missing the space
#after the hash character.
def process_data():
    pass
```

---

## 7.2 Inline Comments

Inline comments appear on the same line as a statement. Use them sparingly. An inline comment must be separated from the statement by at least two spaces. It starts with `# ` (a hash followed by a single space).

Do not use inline comments to state the obvious. They are distracting when they restate what the code already says clearly.

### Correct -- useful inline comment

```python
x = x + 1  # Compensate for border
```

### Wrong -- states the obvious

```python
x = x + 1  # Increment x
```

### Wrong -- not enough spacing (needs at least two spaces before `#`)

```python
x = x + 1 # Compensate for border
```

### Wrong -- missing space after hash

```python
x = x + 1  #Compensate for border
```

---

## 7.3 Documentation Strings (Docstrings)

Write docstrings for all public modules, functions, classes, and methods. Docstrings are not required for non-public methods, but you should have a comment that describes what the method does; place this comment after the `def` line.

PEP 257 describes full docstring conventions. The key rules from PEP 8:

- The closing `"""` of a multiline docstring should be on a line by itself.
- The closing `"""` of a one-liner docstring should be on the same line as the opening `"""`.

### Correct -- one-liner docstring

```python
def square(x):
    """Return the square of x."""
    return x * x
```

### Correct -- multiline docstring

```python
def complex_function(a, b):
    """Return the frobnicated result of a and b.

    Optional plotz says to frobnicate the bizbaz first.
    """
    return frobnicate(a, b)
```

### Wrong -- closing `"""` not on its own line for multiline

```python
def complex_function(a, b):
    """Return the frobnicated result of a and b.

    Optional plotz says to frobnicate the bizbaz first."""
    return frobnicate(a, b)
```

### Wrong -- missing docstring on public function

```python
def public_api_function(data):
    result = transform(data)
    return result
```

### Correct -- non-public method with comment instead of docstring

```python
def _internal_helper(self, value):
    # Normalize value before storing.
    return value.strip().lower()
```

### Reference

See [PEP 257](https://peps.python.org/pep-0257/) for the full docstring conventions, including summary line requirements, multi-line structure, and class docstrings.

---

## Severity Classification

When reviewing code, classify comment violations by severity:

| Severity | Violations |
|---|---|
| **Error** | None |
| **Warning** | Missing docstrings on public modules, functions, classes, or methods. Inline comment without proper spacing (fewer than 2 spaces before `#`). |
| **Suggestion** | Comment quality (vague, unclear, or misleading comments). Obvious inline comments that restate what the code already says. Language choice (non-English comments in a shared codebase). Stale or contradictory comments. |

---

## Pragmatic Consistency

Apply these rules with judgment:

- **Consistency within a module matters most.** If an existing file uses a particular comment style throughout, match it rather than introducing a mix of styles. Then consider updating the whole file in a separate change.
- **Do not add comments just to satisfy a rule.** A comment that says nothing useful is worse than no comment. Write comments that explain *why*, not *what*.
- **Legacy code tolerance.** When modifying a single function in a large legacy file, fix comment style in the code you touch. Do not reformat the entire file in the same change.
- **Team conventions may extend PEP 8.** If the project has a documented convention for docstring format (Google style, NumPy style, Sphinx reST), follow the project convention. PEP 8 defers to PEP 257, which permits multiple formats.

---

## Checklist

Use this checklist when reviewing or writing Python comments:

- [ ] No comments contradict the code they describe.
- [ ] All comments are complete sentences ending with a period.
- [ ] First word of each comment is capitalized (unless it is a lowercase identifier).
- [ ] Block comments are indented to the same level as the code they describe.
- [ ] Block comment lines start with `# ` (hash, space).
- [ ] Block comment paragraphs are separated by a line containing only `#`.
- [ ] Inline comments have at least two spaces before the `#`.
- [ ] Inline comments start with `# ` (hash, space).
- [ ] No inline comments state the obvious.
- [ ] All public modules, functions, classes, and methods have docstrings.
- [ ] One-liner docstrings have closing `"""` on the same line.
- [ ] Multiline docstrings have closing `"""` on its own line.
- [ ] Non-public methods have a descriptive comment after the `def` line if no docstring is provided.
- [ ] Comments are written in English (unless the codebase explicitly targets a non-English audience).
