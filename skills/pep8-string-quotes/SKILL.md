---
name: pep8-string-quotes
description: >-
  Use when writing or reviewing Python string quoting style — single vs double quotes, triple-quoted strings, avoiding unnecessary escapes. Invoke when user mentions "string quotes", "quote style", "triple quotes", "PEP 8 strings", or asks about Python string formatting conventions.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 String Quotes (Section 4)

**Source:** [PEP 8 -- String Quotes](https://peps.python.org/pep-0008/#string-quotes)

## When to Use

- Writing new Python code that contains string literals
- Reviewing Python code for string quoting consistency
- Deciding between single and double quotes in a project
- Cleaning up unnecessary backslash escapes in strings
- Writing or reviewing triple-quoted strings and docstrings

## Rules

### 1. Single vs Double Quotes -- Pick One and Be Consistent

PEP 8 makes **no recommendation** between single-quoted (`'...'`) and double-quoted (`"..."`) strings. Both are equally acceptable. The only rule is: **pick one convention and stick with it** throughout the project or at minimum throughout each module.

```python
# Consistent -- all double quotes (valid choice)
name = "Alice"
greeting = "Hello, world"
path = "/usr/local/bin"

# Consistent -- all single quotes (equally valid choice)
name = 'Alice'
greeting = 'Hello, world'
path = '/usr/local/bin'

# Inconsistent within a module -- avoid this
name = "Alice"
greeting = 'Hello, world'       # Mixed style without reason
path = "/usr/local/bin"
```

When a project uses a formatter such as Black or Ruff, follow whatever quote style that formatter enforces. Black defaults to double quotes; Ruff is configurable. The formatter is the project convention and takes precedence.

### 2. Avoiding Backslash Escapes -- Use the Opposite Quote Style

When a string contains a quote character, use the **opposite** quote style to avoid backslash escapes. This improves readability. PEP 8 explicitly endorses this practice.

```python
# Good -- opposite quotes eliminate escapes
message = "It's a nice day"
html = '<div class="container">'
query = "SELECT * FROM users WHERE name = 'admin'"
text = 'She said "hello" to everyone'

# Avoid -- unnecessary backslash escapes hurt readability
message = 'It\'s a nice day'
html = "<div class=\"container\">"
query = 'SELECT * FROM users WHERE name = \'admin\''
text = "She said \"hello\" to everyone"
```

When a string contains **both** single and double quotes, pick whichever style results in fewer escapes:

```python
# Good -- fewer escapes with double quotes
mixed = "It's a \"quoted\" word"    # 1 escape

# Also acceptable -- fewer escapes with single quotes
mixed = 'It\'s a "quoted" word'     # 1 escape

# Worst -- maximum escapes
mixed = 'It\'s a \'quoted\' word'   # 2 escapes (wrong quote choice)
```

If both styles produce the same number of escapes, fall back to the project's default quote style.

### 3. Triple-Quoted Strings -- Always Use Double Quotes

PEP 8 requires triple-quoted strings to always use **double quotes** (`"""`), consistent with the PEP 257 docstring convention. Never use single-quote triple strings (`'''`).

```python
# Correct -- double-quote triple strings
"""Return a foobang.

Optional plotz says to frobnicate the bizbaz first.
"""

description = """This is a multi-line
string that spans several lines
for readability."""

sql = """
    SELECT u.name, u.email
    FROM users u
    WHERE u.active = true
    ORDER BY u.name
"""

# Wrong -- single-quote triple strings
'''Return a foobang.'''

description = '''This is a multi-line
string that spans several lines
for readability.'''
```

This rule applies to **all** triple-quoted strings, not only docstrings: multi-line string literals, SQL queries, template strings, and any other use of triple quotes.

## Severity Classification

| Severity   | Rule                                                                 |
|------------|----------------------------------------------------------------------|
| Error      | None (PEP 8 makes no hard rules for string quotes)                   |
| Warning    | Triple-quoted strings using single quotes (`'''`) instead of double  |
| Suggestion | Inconsistent quote style within a module                             |
| Suggestion | Unnecessary backslash escapes when the opposite quote style would work |

## Pragmatic Consistency

PEP 8's "foolish consistency" principle (Section 2) applies to string quotes:

1. **Consistency with PEP 8 is important** -- but PEP 8 makes no quote preference, so this only applies to triple-quoted strings.
2. **Consistency within a project is more important** -- if the project uses single quotes everywhere, continue using single quotes.
3. **Consistency within a module or function is most important** -- never mix quote styles arbitrarily within one file.

When reviewing code:

- If the project uses a formatter (Black, Ruff, autopep8) that enforces a quote style, **follow the formatter**. Do not flag its output as inconsistent.
- If `pyproject.toml`, `ruff.toml`, or `setup.cfg` specifies a quote preference, respect it.
- If the surrounding codebase consistently uses one style, recommend that style even if you personally prefer the other.
- Only flag quote style as a suggestion, never as an error or warning (except for triple-quoted single quotes).

## Checklist

When writing or reviewing Python string quotes, verify each of the following:

1. **Quote consistency** -- The module uses a single quote style (either `'` or `"`) for regular strings, switching only to avoid escapes.
2. **No unnecessary escapes** -- Every backslash-escaped quote in the file is checked: could the opposite quote style eliminate it? If yes, flag it.
3. **Triple quotes use double quotes** -- Every `'''` triple-quoted string is flagged; recommend changing to `"""`.
4. **Project convention respected** -- Check for formatter config (`pyproject.toml` `[tool.black]` or `[tool.ruff.format]`, `.editorconfig`, `setup.cfg`) and ensure the code matches the configured quote style.
5. **Docstrings use `"""`** -- All docstrings (module, class, function, method) use double-quote triple strings per PEP 257.
