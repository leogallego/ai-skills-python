---
name: pep8-code-layout
description: >-
  Use when writing or reviewing Python code layout — indentation, line length, blank lines, tabs vs spaces, continuation lines, binary operator breaks, source encoding. Invoke when user mentions "indentation", "line length", "blank lines", "code layout", "PEP 8 layout", or asks to format Python code structure.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Code Layout

This skill enforces PEP 8 Sections 2 and 3.1--3.6 for Python code layout. It operates in two modes:

- **Writing mode**: Apply all rules as you write new Python code.
- **Review mode**: Scan existing code and report every violation with its location, severity, and the corrected form.

When reviewing, report each violation on a separate line with the format:
`<file>:<line> [<severity>] <rule> — <description>`

---

## 1. A Foolish Consistency Is the Hobgoblin of Little Minds (Section 2)

### Consistency hierarchy

Consistency matters, but it is not absolute. The hierarchy from weakest to strongest is:

1. **This style guide** (PEP 8) -- the baseline.
2. **Project-level conventions** -- override PEP 8 when the project has an established style.
3. **Module or function consistency** -- the most important level. Within a single module or function, be consistent above all.

### When to break the rules

Do **not** flag code that deviates from PEP 8 if any of the following apply:

- Applying the guideline would make the code **less readable**, even to someone who is used to PEP 8.
- The surrounding code already uses a different style and **consistency with that code** is more important.
- The code **predates** the introduction of the guideline and there is no other reason to change it.
- The code needs to remain **backwards compatible** with older Python versions that do not support the feature suggested by the style guide.

When any of these apply, **note** the deviation rather than flagging it as a violation.

---

## 2. Indentation (Section 3.1)

### Rule: Use 4 spaces per indentation level

Every indentation level must use exactly 4 spaces. Never use 2, 3, or 8 spaces per level.

**Correct:**

```python
def greeting(name):
    if name:
        print(f"Hello, {name}!")
    else:
        print("Hello, stranger!")
```

**Incorrect:**

```python
# 2-space indentation -- violates PEP 8
def greeting(name):
  if name:
    print(f"Hello, {name}!")
  else:
    print("Hello, stranger!")
```

### Rule: Continuation lines with vertical alignment

When a function call or definition spans multiple lines, align wrapped arguments with the opening delimiter.

**Correct -- aligned with opening delimiter:**

```python
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
```

**Incorrect -- arguments on first line without alignment:**

```python
foo = long_function_name(var_one, var_two,
    var_three, var_four)
```

### Rule: Hanging indent (no arguments on the first line)

When using hanging indent, there must be no arguments on the first line, and the continuation line must use one extra indentation level to distinguish it from the function body.

**Correct -- hanging indent:**

```python
foo = long_function_name(
    var_one, var_two,
    var_three, var_four,
)
```

**Correct -- hanging indent in a function definition with extra indentation:**

```python
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

**Incorrect -- indentation not distinguishable from function body:**

```python
def long_function_name(
    var_one, var_two, var_three,
    var_four):
    print(var_one)
```

In the incorrect example, `var_one` and `print(var_one)` are at the same indentation level, making the code ambiguous.

### Rule: Hanging indent for function calls

Function calls with hanging indent follow the same principle.

**Correct:**

```python
result = some_function_that_takes_arguments(
    "argument_one",
    "argument_two",
    "argument_three",
)
```

### Rule: Multiline conditionals

When the conditional part of an `if` statement is long enough to require multiple lines, note that the combination of `if ` plus a space plus an opening parenthesis creates a natural 4-space indent. This can produce a visual conflict with the indented suite of code inside the `if` statement. PEP 8 does not mandate a specific approach but accepts several options.

**Acceptable -- no extra indentation (visually ambiguous but allowed):**

```python
if (this_is_one_thing
    and that_is_another_thing):
    do_something()
```

**Better -- add a clarifying comment to separate condition from body:**

```python
if (this_is_one_thing
    and that_is_another_thing):
    # Both conditions are met, proceed.
    do_something()
```

**Better -- extra indentation on the condition continuation:**

```python
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

### Rule: Closing brackets on multiline constructs

The closing bracket on a multiline construct may either line up under the first non-whitespace character of the last item, or under the first character of the line that starts the construct. Both are valid.

**Correct -- closing bracket under last item's first non-whitespace character:**

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
```

**Also correct -- closing bracket under the opening line:**

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
```

**Both styles are valid for function arguments as well:**

```python
result = some_function(
    arg_one, arg_two,
    arg_three, arg_four,
    )

result = some_function(
    arg_one, arg_two,
    arg_three, arg_four,
)
```

---

## 3. Tabs or Spaces (Section 3.2)

### Rule: Spaces are the preferred indentation method

Use spaces for indentation. Tabs should only be used to remain consistent with code that is already indented with tabs.

**Correct:**

```python
def calculate_area(width, height):
    return width * height
```

**Incorrect -- using tabs (shown as literal tab characters):**

```python
# Each indentation level uses a tab character instead of 4 spaces.
# This violates PEP 8 for new code.
def calculate_area(width, height):
	return width * height
```

### Rule: Python 3 disallows mixing tabs and spaces

Python 3 raises a `TabError` if you mix tabs and spaces for indentation within the same file. This is not merely a style rule -- the interpreter enforces it.

**Incorrect -- mixing tabs and spaces (Python 3 will raise TabError):**

```python
def process(data):
    first_step = transform(data)    # indented with spaces
	second_step = validate(first_step)  # indented with tab -- TabError
    return second_step
```

---

## 4. Maximum Line Length (Section 3.3)

### Rule: Limit all lines to a maximum of 79 characters

All code lines must be at most 79 characters long. This allows side-by-side file viewing in editors and prevents wrapping in code review tools.

### Rule: Limit docstrings and comments to 72 characters

Docstrings, block comments, and flowing long-form text must be limited to 72 characters per line.

**Correct -- code at 79 chars max:**

```python
total_revenue = (
    quarterly_sales + annual_bonus + investment_returns
)
```

**Correct -- docstring at 72 chars max:**

```python
def calculate_tax(income, deductions):
    """Calculate the effective tax rate after all applicable
    deductions have been subtracted from the gross income.

    This function uses the progressive tax bracket system
    defined in the current fiscal year's tax code.
    """
    return (income - deductions) * TAX_RATE
```

**Incorrect -- line exceeds 79 characters:**

```python
total_revenue = quarterly_sales + annual_bonus + investment_returns + misc_income + residual_payments
```

### Rule: Teams may agree on 99 characters for code, but docstrings stay at 72

Some teams choose to allow lines up to 99 characters. Even when this team agreement is in place, docstrings and comments must remain at 72 characters.

### Rule: Prefer implicit continuation over backslashes

Use parentheses, brackets, or braces to wrap long lines. Implicit line continuation inside these delimiters is preferred over backslash continuation.

**Correct -- implicit continuation with parentheses:**

```python
total = (
    first_variable
    + second_variable
    + third_variable
    - fourth_variable
)
```

**Correct -- implicit continuation in function calls:**

```python
if (
    width > minimum_width
    and height > minimum_height
    and depth > minimum_depth
):
    resize_container()
```

**Correct -- implicit continuation in list:**

```python
allowed_hosts = [
    "example.com",
    "api.example.com",
    "staging.example.com",
]
```

**Acceptable -- backslash continuation for `with` statements:**

When `with` statements require multiple context managers and implicit continuation cannot be used cleanly, backslash continuation is acceptable.

```python
with open("/path/to/input/file") as input_file, \
     open("/path/to/output/file", "w") as output_file:
    output_file.write(input_file.read())
```

**Incorrect -- backslash where parentheses would work:**

```python
total = first_variable + \
        second_variable + \
        third_variable - \
        fourth_variable
```

---

## 5. Should a Line Break Before or After a Binary Operator? (Section 3.4)

### Rule: Break before the operator (Knuth style) for new code

For new code, break the line **before** the binary operator. This follows Donald Knuth's style and makes it easier to match operators with their operands because the operator sits at the beginning of the new line, visually aligned.

**Correct -- break before operator (Knuth style):**

```python
income = (
    gross_wages
    + taxable_interest
    + (dividends - qualified_dividends)
    - ira_deduction
    - student_loan_interest
)
```

**Incorrect -- break after operator (old style):**

```python
income = (
    gross_wages +
    taxable_interest +
    (dividends - qualified_dividends) -
    ira_deduction -
    student_loan_interest
)
```

In the correct version, the eye can immediately see which operator connects each term because the operator starts the line. In the incorrect version, operators are buried at the end of lines where they are easy to miss.

### Note on existing code

For existing code that already uses the after-operator style consistently, it is acceptable to keep that style for local consistency. Do not rewrite large blocks of code solely to change operator position. Flag it as a **suggestion**, not a warning or error.

---

## 6. Blank Lines (Section 3.5)

### Rule: Surround top-level definitions with two blank lines

Top-level function definitions and class definitions must be surrounded by exactly two blank lines.

**Correct:**

```python
import os


def first_function():
    return 1


def second_function():
    return 2


class MyClass:
    pass
```

**Incorrect -- only one blank line between top-level definitions:**

```python
import os

def first_function():
    return 1

def second_function():
    return 2

class MyClass:
    pass
```

### Rule: Surround method definitions with one blank line

Method definitions inside a class must be surrounded by a single blank line.

**Correct:**

```python
class Calculator:

    def add(self, a, b):
        return a + b

    def subtract(self, a, b):
        return a - b

    def multiply(self, a, b):
        return a * b
```

**Incorrect -- no blank lines between methods:**

```python
class Calculator:
    def add(self, a, b):
        return a + b
    def subtract(self, a, b):
        return a - b
    def multiply(self, a, b):
        return a * b
```

### Rule: Extra blank lines may be used sparingly to separate groups of related functions

Top-level function groups may use extra blank lines to create visual separation between logical groups.

**Correct:**

```python
# --- Input/Output functions ---

def read_config(path):
    pass


def write_config(path, data):
    pass


# --- Processing functions ---

def transform_data(raw_data):
    pass


def validate_data(data):
    pass
```

### Rule: Use blank lines in functions sparingly to indicate logical sections

Within a function body, blank lines may be used sparingly to separate logical sections of code.

**Correct:**

```python
def process_order(order):
    # Validate order
    if not order.is_valid():
        raise ValueError("Invalid order")

    # Calculate totals
    subtotal = sum(item.price for item in order.items)
    tax = subtotal * TAX_RATE
    total = subtotal + tax

    # Persist and notify
    order.total = total
    order.save()
    notify_customer(order)
```

---

## 7. Source File Encoding (Section 3.6)

### Rule: Code in the core Python distribution must use UTF-8 and should not have an encoding declaration

UTF-8 is the default encoding for Python 3 source files. Do not add an encoding declaration (`# -*- coding: utf-8 -*-`) unless you have a specific reason.

**Correct -- no encoding declaration needed:**

```python
"""Module for handling user profiles."""


def get_display_name(user):
    return user.full_name or user.username
```

**Unnecessary -- encoding declaration for UTF-8 in Python 3:**

```python
# -*- coding: utf-8 -*-
"""Module for handling user profiles."""


def get_display_name(user):
    return user.full_name or user.username
```

### Rule: Use non-ASCII characters sparingly

Non-ASCII characters should appear only where they are essential -- for example, place names, person names, or internationalization test data.

**Acceptable -- non-ASCII for person names and place names:**

```python
contributors = [
    "Guido van Rossum",
    "Luciano Ramalho",
    "Sebastien Jodogne",
]

cities = {
    "DE": "Munchen",
    "JP": "Tokyo",
    "BR": "Sao Paulo",
}
```

### Rule: All identifiers must use ASCII-only characters and should be English words

Variable names, function names, class names, and all other identifiers must use only ASCII characters. English words are preferred for identifiers in projects shared with an international audience.

**Correct:**

```python
def calculate_average(values):
    total = sum(values)
    count = len(values)
    return total / count
```

**Incorrect -- non-ASCII identifiers:**

```python
def berechne_durchschnitt(werte):
    gesamt = sum(werte)
    anzahl = len(werte)
    return gesamt / anzahl
```

While the incorrect example is syntactically valid in Python 3, it is not recommended for codebases with international contributors because non-English identifiers reduce readability for the broader community.

---

## Severity Classification

When reviewing code, classify each violation by severity:

### Error (must fix)

These cause runtime failures or represent unambiguous violations:

- **Mixed tabs and spaces** -- Python 3 raises `TabError`; this is never acceptable.
- **Indentation not a multiple of 4 spaces** -- Breaks the fundamental indentation rule and can cause logic errors.

### Warning (should fix)

These are clear PEP 8 violations that do not cause runtime errors but harm readability and consistency:

- **Lines exceeding 79 characters** (or the team-agreed limit).
- **Continuation lines misaligned** -- Arguments not aligned with the opening delimiter or not using a proper hanging indent.
- **Wrong blank line count** -- Missing the required 2 blank lines around top-level definitions or 1 blank line around method definitions.
- **Docstring/comment lines exceeding 72 characters.**

### Suggestion (consider fixing)

These are stylistic preferences where PEP 8 offers multiple acceptable options or where existing code consistency takes precedence:

- **Closing bracket style** -- Both "under last item" and "under opening line" are valid. Note which style the project uses, but do not flag as a violation.
- **Binary operator break style in existing code** -- If existing code consistently breaks after the operator, note the deviation from the recommended Knuth style but do not flag it as a warning. Only flag it if the code is inconsistent within itself.
- **Encoding declaration present** -- Unnecessary but not harmful in Python 3. Suggest removal during refactoring.

---

## Pragmatic Consistency

### Project conventions take precedence

When a project has an established style that deviates from PEP 8, follow the project's conventions. PEP 8 itself states that consistency within a project is more important than consistency with PEP 8. For example:

- If the project uses 99-character line limits, do not flag lines between 80 and 99 characters.
- If the project uses tabs (rare but possible in legacy code), use tabs within that project.
- If the project breaks after binary operators throughout, maintain that style.

### The consistency hierarchy in practice

When you encounter a conflict between PEP 8 and existing code:

1. **Within a function or method**: Always match the surrounding code. Never introduce a different style mid-function.
2. **Within a module**: Follow the module's established conventions. If the module uses one blank line between top-level functions consistently, maintain that within the module (but flag it as a suggestion for the module as a whole).
3. **Within a project**: Follow the project's style guide, `.editorconfig`, `pyproject.toml` settings, or linter configuration.
4. **No project convention**: Follow PEP 8.

### When to note vs. when to flag

- **Note (informational)**: The code deviates from PEP 8 but is consistent with the surrounding code or project conventions. Mention the deviation without marking it as a violation.
- **Flag (violation)**: The code is inconsistent with both PEP 8 and the surrounding code, or the violation causes readability or runtime problems regardless of context (e.g., mixed tabs and spaces).

---

## Checklist

Apply this checklist when writing or reviewing Python code for layout compliance:

1. **Indentation uses 4 spaces consistently** -- No tabs, no 2-space or 8-space indentation. Every indentation level is exactly 4 spaces.
2. **No mixed tabs and spaces** -- The entire file uses one or the other (spaces for new code). Python 3 enforces this at the interpreter level.
3. **Line lengths are within limits** -- Code lines are at most 79 characters (or the team-agreed limit). Comments and docstrings are at most 72 characters.
4. **Continuation lines are properly aligned** -- Arguments are either aligned with the opening delimiter or use a hanging indent with an extra indentation level to distinguish them from the function body.
5. **Binary operators break before the operator** -- In new code, multi-line expressions break before `+`, `-`, `*`, `/`, `and`, `or`, and other binary operators.
6. **Blank lines follow the rules** -- Two blank lines surround top-level function and class definitions. One blank line surrounds method definitions inside classes.
7. **Source encoding is UTF-8** -- No encoding declaration is needed in Python 3. If present, consider removing it.
8. **Identifiers are ASCII-only** -- All variable names, function names, class names, and other identifiers use only ASCII characters and preferably English words.
