---
name: pep8-trailing-commas
description: >-
  Use when writing or reviewing Python trailing comma usage — singleton tuples, multi-line collections, VCS-friendly formatting. Invoke when user mentions "trailing commas", "tuple comma", "PEP 8 commas", or asks about Python collection formatting.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Trailing Commas (Section 6)

## When to Use

- Writing or reviewing Python code that defines tuples, lists, dicts, sets, or function arguments
- Creating singleton tuples where the trailing comma is syntactically required
- Formatting multi-line collections for clean version control diffs
- User asks about "trailing commas", "tuple comma", "PEP 8 commas", or Python collection formatting

## Rules

### Rule 1: Singleton Tuples (Mandatory Trailing Comma)

A single-element tuple **must** have a trailing comma inside parentheses. Without it, the parentheses are just grouping operators and the value is not a tuple.

```python
# Correct — trailing comma inside parentheses makes this a tuple
FILES = ('setup.cfg',)

# Wrong — this is just a string in parentheses, not a tuple
FILES = ('setup.cfg')

# Wrong — trailing comma without parentheses is legal but confusing
FILES = 'setup.cfg',
```

The parenthesized form with trailing comma `('setup.cfg',)` is the only correct way to write a singleton tuple. The bare trailing comma form `'setup.cfg',` is syntactically valid but visually misleading and should be avoided.

### Rule 2: Multi-line Collections (Trailing Comma Recommended)

When a collection spans multiple lines, add a trailing comma after the last item. Place each item on its own line, and put the closing delimiter on a separate line.

```python
# Correct — each item on its own line, trailing comma, closing on its own line
FILES = [
    'setup.cfg',
    'tox.ini',
]

initialize(
    FILES,
    error=True,
)

MY_CONFIG = {
    'debug': True,
    'verbose': False,
}
```

```python
# Wrong — trailing comma on same line as closing bracket
FILES = ['setup.cfg', 'tox.ini',]

initialize(FILES, error=True,)

MY_CONFIG = {'debug': True, 'verbose': False,}
```

The multi-line format with trailing comma serves two purposes:
1. Adding a new item only requires adding one line (no editing the previous last line to add a comma).
2. The closing delimiter on its own line makes the block structure visually clear.

### Rule 3: Why Trailing Commas Matter for VCS Diffs

Trailing commas in multi-line collections produce cleaner diffs when items are added or removed. This matters for code review and version control history.

**Without trailing comma** -- adding an item touches two lines:

```diff
 FILES = [
     'setup.cfg',
-    'tox.ini'
+    'tox.ini',
+    'nox.ini'
 ]
```

The diff shows `tox.ini` as modified (comma added) even though its value did not change. This clutters blame output and review diffs.

**With trailing comma** -- adding an item touches only one line:

```diff
 FILES = [
     'setup.cfg',
     'tox.ini',
+    'nox.ini',
 ]
```

Only the genuinely new line appears in the diff. The same benefit applies to reordering and removing items.

### Rule 4: Single-line Collections

For collections that fit on a single line, trailing commas are optional. PEP 8 does not require or prohibit them.

```python
# Both are acceptable for single-line collections
FILES = ['setup.cfg', 'tox.ini']
FILES = ['setup.cfg', 'tox.ini',]  # Also acceptable, but less common
```

The key rule is: **never** mix the trailing-comma-with-closing-delimiter-on-same-line style in what should be a multi-line collection. If you use trailing commas, commit to the full multi-line format.

### Rule 5: Function Definitions and Calls

The same trailing comma rules apply to function signatures and calls when they span multiple lines.

```python
# Correct — multi-line function definition with trailing comma
def long_function_name(
    var_one: int,
    var_two: str,
    var_three: float,
) -> bool:
    ...

# Correct — multi-line function call with trailing comma
result = long_function_name(
    var_one=1,
    var_two='hello',
    var_three=3.14,
)
```

```python
# Wrong — multi-line without trailing comma
def long_function_name(
    var_one: int,
    var_two: str,
    var_three: float
) -> bool:
    ...
```

## Severity Classification

| Severity   | Rule                                                              | Rationale                                          |
|------------|-------------------------------------------------------------------|----------------------------------------------------|
| Error      | Missing trailing comma on singleton tuple: `(x)` when tuple is intended | Changes semantics -- `(x)` is not a tuple          |
| Warning    | Missing trailing comma in multi-line collection or function signature | Produces noisier VCS diffs and increases merge conflicts |
| Suggestion | Trailing comma style in single-line collections                   | No functional or diff impact; purely a style choice |

## Pragmatic Consistency

PEP 8 Section 2 states: "A foolish consistency is the hobgoblin of little minds."

When reviewing code, apply these rules with the following priority:

1. **Consistency within the module or function** is most important.
2. **Consistency within the project** is more important than PEP 8 compliance.
3. **Consistency with PEP 8** is important but yields to project conventions.

If a project consistently omits trailing commas in multi-line collections, do not add them to a single file. Instead, note the PEP 8 recommendation and follow the project convention. Only flag as a warning when the code is inconsistent with **both** PEP 8 and the project's own style.

If a project uses a formatter such as Black, Ruff, or autopep8, defer to the formatter's trailing comma behavior. Do not fight the formatter.

## Checklist

When writing or reviewing Python code for trailing comma compliance:

1. **Singleton tuples**: Verify every single-element tuple uses the `(value,)` form. Search for patterns like `(expr)` assigned to variables or passed as arguments where a tuple is intended.
2. **Multi-line collections**: Confirm every list, dict, set, tuple, function definition, and function call that spans multiple lines has a trailing comma after the last item.
3. **Closing delimiter placement**: Ensure the closing bracket, brace, or parenthesis is on its own line when the collection uses trailing commas.
4. **No same-line trailing commas**: Reject patterns like `[a, b,]` or `func(a, b,)` on a single line -- either drop the trailing comma or expand to multi-line format.
5. **Project conventions**: Check if the project uses a formatter or has an established convention that differs from PEP 8. Follow the project convention and note any deviation.
6. **Diff impact**: When adding items to an existing collection, check whether adding a trailing comma to the previous last item would improve future diffs.
