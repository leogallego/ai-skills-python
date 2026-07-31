---
name: pep8-whitespace
description: >-
  Use when writing or reviewing Python whitespace in expressions and statements — spacing around operators, inside delimiters, trailing whitespace, compound statements, annotation spacing. Invoke when user mentions "whitespace", "spacing", "operators", "PEP 8 whitespace", or asks about Python expression formatting.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Whitespace in Expressions and Statements

This skill enforces PEP 8 Section 5 rules for whitespace in Python expressions and statements. Apply every rule below when writing, reviewing, or refactoring Python code.

---

## 5.1 Pet Peeves

### Rule 1: No spaces inside parentheses, brackets, or braces

Immediately inside parentheses, brackets, or braces, do not add spaces.

```python
# Correct
spam(ham[1], {eggs: 2})

# Wrong
spam( ham[ 1 ], { eggs: 2 } )
```

### Rule 2: No space before a trailing comma in a single-element tuple

```python
# Correct
foo = (0,)

# Wrong
bar = (0, )
```

### Rule 3: No space before comma, semicolon, or colon

```python
# Correct
if x == 4: print(x, y); x, y = y, x

# Wrong
if x == 4 : print(x , y) ; x , y = y , x
```

### Rule 4: Slice colon spacing -- equal amount on each side, no space for simple slices

In a slice the colon acts like a binary operator and must have equal amounts of space around it. When a slice parameter is omitted, the space is omitted. For simple slices use no spaces. For complex expressions, use a single space on each side of the colon.

```python
# Correct
ham[1:9]
ham[1:9:3]
ham[:9:3]
ham[1::3]
ham[1:9:]
ham[lower+offset : upper+offset]
ham[lower+offset : upper+offset : step]

# Wrong
ham[1: 9]
ham[1 :9]
ham[1:9 :3]
ham[lower + offset:upper + offset]
ham[lower + offset : upper + offset :step]
```

### Rule 5: No space before function call parentheses

```python
# Correct
spam(1)

# Wrong
spam (1)
```

### Rule 6: No space before indexing or slicing brackets

```python
# Correct
dct['key']
lst[0]

# Wrong
dct ['key']
lst [0]
```

### Rule 7: No extra spaces to align assignments

Do not use more than one space around an assignment (or other) operator to align it with another.

```python
# Correct
x = 1
y = 2
long_variable = 3

# Wrong
x             = 1
y             = 2
long_variable = 3
```

---

## 5.2 Other Recommendations

### Rule 8: Avoid trailing whitespace anywhere

Trailing whitespace is invisible and can cause confusion. Remove it everywhere. Many editors can be configured to strip trailing whitespace on save.

```python
# Correct
x = 1

# Wrong (invisible trailing spaces after the value)
x = 1   
```

### Rule 9: Surround binary operators with a single space on each side

This applies to assignment (`=`), augmented assignment (`+=`, `-=`, etc.), comparison (`==`, `<`, `>`, `!=`, `<=`, `>=`), membership (`in`, `not in`), identity (`is`, `is not`), and Boolean operators (`and`, `or`, `not`).

```python
# Correct
x = 1
x += 1
if x == 4:
    pass
if x is not None:
    pass
if x in collection:
    pass
result = a and b or c

# Wrong
x=1
x+=1
if x==4:
    pass
if x is not  None:
    pass
```

### Rule 10: Mixed-priority operators -- use spacing to indicate grouping

When combining operators of different priorities, consider omitting spaces around the lower-priority operators to make grouping visually clear. Use your judgment, but never use more than one space, and always have the same amount of whitespace on both sides of a binary operator.

```python
# Correct -- tighter binding is visually grouped
x = x*2 - 1
hypot2 = x*x + y*y
c = (a+b) * (a-b)

# Wrong -- equal spacing obscures priority
x = x * 2 - 1
hypot2 = x * x + y * y
c = (a + b) * (a - b)
```

### Rule 11: Function annotation spacing

Use normal colon rules for annotations and surround the arrow (`->`) with spaces.

```python
# Correct
def munge(input: AnyStr): ...
def munge() -> PosInt: ...

# Wrong
def munge(input:AnyStr): ...
def munge()->PosInt: ...
```

### Rule 12: Default parameter values without annotations -- no spaces around `=`

```python
# Correct
def complex(real, imag=0.0):
    return magic(r=real, i=imag)

# Wrong
def complex(real, imag = 0.0):
    return magic(r = real, i = imag)
```

### Rule 13: Default parameter values WITH annotations -- spaces around `=`

When combining an argument annotation with a default value, use spaces around the `=` sign.

```python
# Correct
def munge(sep: AnyStr = None): ...
def munge(input: AnyStr, sep: AnyStr = None, limit=1000): ...

# Wrong
def munge(input: AnyStr=None): ...
def munge(input: AnyStr, limit =1000): ...
```

### Rule 14: No compound statements on a single line

Put each statement on its own line.

```python
# Correct
if foo == 'blah':
    do_blah_thing()
do_one()
do_two()
do_three()

# Discouraged
if foo == 'blah': do_blah_thing()
do_one(); do_two(); do_three()
```

### Rule 15: Never put multi-clause compound statements on one line

This is always unacceptable regardless of context.

```python
# NEVER do this
if foo == 'blah': one(); two(); three()

# NEVER do this either
if foo == 'blah': do_blah_thing()
else: do_non_blah_thing()
```

---

## Severity Classification

All whitespace rules are stylistic; none cause runtime errors. Classify findings as follows:

| Severity     | Rules                                                                                             |
|--------------|---------------------------------------------------------------------------------------------------|
| **Warning**  | Spaces inside delimiters (1), missing spaces around operators (9), trailing whitespace (8), compound statements on one line (14, 15), missing annotation spacing (11), wrong default value spacing (12, 13) |
| **Suggestion** | Operator grouping by priority (10), alignment style (7)                                         |

There are no error-level findings -- all whitespace violations are style issues.

---

## Pragmatic Consistency

> A Foolish Consistency is the Hobgoblin of Little Minds -- PEP 8

When applying these whitespace rules, keep these principles in mind:

- **Consistency within a module matters most.** If a file already uses a specific whitespace convention throughout, follow it rather than creating a mixed style, then clean up the whole file as a separate change.
- **Readability is the goal, not the rule.** Operator priority spacing (Rule 10) is explicitly a judgment call. Use it when it genuinely aids readability; omit it when it does not.
- **Do not break existing code for whitespace alone.** When making a functional change, fix whitespace in the lines you touch. Avoid pure whitespace commits that obscure `git blame`.
- **Automated formatters are your friend.** Tools like `black`, `autopep8`, and `ruff format` handle most of these rules automatically. Prefer tool enforcement over manual review for ongoing compliance.
- **When in doubt, add a space.** Missing spaces harm readability more than extra spaces do. The one exception is inside delimiters (Rules 1-2) and before call/index brackets (Rules 5-6), where extra spaces are distracting.

---

## Checklist

Use this checklist when reviewing Python code for PEP 8 whitespace compliance:

- [ ] No spaces immediately inside `()`, `[]`, or `{}`
- [ ] No space before trailing comma in single-element tuples
- [ ] No space before `,`, `;`, or `:`
- [ ] Slice colons have equal spacing on both sides (none for simple, one space for complex)
- [ ] No space before `(` in function calls or `[` in indexing
- [ ] No extra spaces used to align assignment operators
- [ ] No trailing whitespace on any line
- [ ] Single space around binary operators (`=`, `+=`, `==`, `<`, `>`, `!=`, `in`, `not in`, `is`, `is not`, `and`, `or`, `not`)
- [ ] Operator priority spacing is used consistently and aids readability
- [ ] Function annotations use a space after the colon and spaces around `->`
- [ ] No spaces around `=` in default parameter values (without annotations)
- [ ] Spaces around `=` in default parameter values when annotations are present
- [ ] No compound statements on a single line
- [ ] No multi-clause compound statements on a single line
