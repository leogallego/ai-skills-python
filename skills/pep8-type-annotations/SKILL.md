---
name: pep8-type-annotations
description: >-
  Use when writing or reviewing Python type annotations — function annotations (PEP 484), variable annotations (PEP 526), annotation spacing, type checker directives. Invoke when user mentions "type hints", "annotations", "type annotations", "PEP 484", "PEP 526", "typing", or asks about Python type annotation formatting.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Type Annotations (Sections 10 and 11)

## When to Use

- Writing new Python functions, methods, or classes and deciding how to annotate them
- Reviewing existing code for correct type annotation formatting
- Checking spacing around annotation colons, arrows, and default values
- Adding or updating type hints on public interfaces
- Working with stub files (.pyi) for type information distribution
- Configuring or using type checker directives (`# type: ignore`)

## Rules

### Function Annotations (PEP 484 — Section 10)

PEP 484 introduced the standard syntax for function annotations (type hints). Type checkers are optional tools -- Python interpreters should not alter their behavior based on annotations and should not raise errors at runtime because of annotations.

#### Use PEP 484 syntax for function annotations

```python
# Correct
def greeting(name: str) -> str:
    return "Hello " + name

def process(items: list[int], flag: bool = True) -> None:
    pass

def retry(func: Callable[..., T], attempts: int = 3) -> T:
    ...
```

#### Annotation spacing: colon and arrow

There must be no space before the colon, one space after the colon, and spaces around the `->` arrow.

```python
# Correct
def munge(input: AnyStr): ...
def munge() -> PosInt: ...
def munge(input: AnyStr, sep: AnyStr = None) -> str: ...

# Wrong — missing spaces around colon or arrow
def munge(input:AnyStr): ...       # No space after colon
def munge()->PosInt: ...            # No spaces around arrow
def munge(input :AnyStr): ...      # Space before colon
```

#### Type checker directives

Use `# type: ignore` comments to suppress type checker warnings on specific lines when the type checker produces a false positive or when a deliberate dynamic pattern cannot be expressed in the type system.

```python
# Suppress a specific type checker warning
value = some_dynamic_call()  # type: ignore

# Prefer targeted suppression when your type checker supports it
value = some_dynamic_call()  # type: ignore[attr-defined]
```

### Variable Annotations (PEP 526 — Section 11)

PEP 526 introduced syntax for variable annotations. The spacing rules mirror function annotations.

#### Spacing rules for variable annotations

- Single space after the colon
- No space before the colon
- Spaces around `=` when a default value is present in an annotated assignment

```python
# Correct
code: int
code: int = 0

class Point:
    coords: Tuple[int, int]
    label: str = '<unknown>'

# Wrong — colon spacing
code:int          # No space after colon
code : int        # Space before colon

# Wrong — equality spacing in annotated assignment
class Test:
    result: int=0  # No spaces around equality sign
```

#### Contrast with unannotated defaults

When there is no annotation, PEP 8 requires no spaces around `=` for default parameter values. When an annotation IS present, spaces around `=` are required. This distinction is critical.

```python
# Correct — annotated defaults get spaces around =
def munge(sep: AnyStr = None): ...
def munge(input: AnyStr, sep: AnyStr = None, limit=1000): ...

# Wrong — missing spaces around = in annotated default
def munge(input: AnyStr=None): ...

# Wrong — spaces around = in unannotated default
def munge(input: AnyStr, limit = 1000): ...
```

Note in the correct example above: `limit=1000` has no spaces because it lacks an annotation, while `sep: AnyStr = None` has spaces because it has one.

### Stub Files (.pyi)

Stub files (`.pyi`) are the preferred mechanism for distributing type information separately from implementation code. Type checkers read stub files in preference to the corresponding `.py` files.

Use stub files when:
- Distributing type information for a library that does not include inline annotations
- The implementation code must remain compatible with older Python versions that do not support annotation syntax
- Annotations would clutter the implementation code significantly

```python
# my_module.pyi — stub file
def connect(host: str, port: int = 443, *, timeout: float = 30.0) -> Connection: ...
def query(conn: Connection, sql: str, params: Sequence[Any] = ()) -> list[Row]: ...

class Connection:
    host: str
    port: int
    def close(self) -> None: ...
```

### Summary of spacing rules

| Context | Correct | Wrong |
|---|---|---|
| Function annotation colon | `def f(x: int):` | `def f(x:int):` or `def f(x :int):` |
| Return type arrow | `def f() -> int:` | `def f()->int:` |
| Variable annotation colon | `x: int` | `x:int` or `x : int` |
| Annotated default value | `def f(x: int = 0):` | `def f(x: int=0):` |
| Unannotated default value | `def f(x=0):` | `def f(x = 0):` |
| Annotated variable with value | `x: int = 0` | `x: int=0` |

## Severity Classification

| Severity | Rules |
|---|---|
| **Error** | None -- annotations are entirely optional per PEP 484. No annotation formatting issue constitutes a runtime error. |
| **Warning** | Missing space after colon in annotation (`x:int`). Space before colon in annotation (`x : int`). Missing spaces around `=` in annotated default (`x: int=0`). Extra spaces around `=` in unannotated default (`limit = 1000`). Missing spaces around return arrow (`->` without surrounding spaces). |
| **Suggestion** | Adding type annotations to public functions, methods, and classes. Using stub files for distributing type information. Using targeted `# type: ignore[code]` instead of bare `# type: ignore`. |

## Pragmatic Consistency

PEP 8 Section 2 establishes the "foolish consistency" principle. Apply it to type annotations as follows:

1. **Consistency with PEP 8 is important** -- follow the spacing rules above for new code.
2. **Consistency within a project is more important** -- if the project omits type annotations entirely, do not force them in. If the project uses a specific annotation style (e.g., `from __future__ import annotations` with string-form hints), follow that convention.
3. **Consistency within a module or function is most important** -- if a module annotates some functions but not others, either annotate all public functions or none. Do not leave a mixed state without reason.

When reviewing code, if the surrounding codebase consistently uses a different convention:
- Note the PEP 8 recommendation
- Note the project convention
- Recommend following the project convention
- Only flag as warning if the code is inconsistent with BOTH PEP 8 and the project convention

Type annotations are explicitly optional. Never treat the absence of annotations as a violation. The value of annotations increases with codebase size and team size -- for small scripts, omitting them is perfectly acceptable.

## Checklist

When writing or reviewing Python code for type annotation compliance:

1. **Check annotation spacing** -- verify no space before colons, one space after colons, and spaces around `->` in all function signatures and variable annotations.
2. **Check default value spacing** -- verify spaces around `=` when an annotation is present (`x: int = 0`), and no spaces around `=` when no annotation is present (`x=0`).
3. **Check consistency** -- if the module or project uses type annotations, verify they are applied consistently across all public interfaces in the same scope.
4. **Check variable annotations** -- verify class-level and module-level variable annotations follow the same colon and equals spacing rules as function annotations.
5. **Check type: ignore usage** -- verify `# type: ignore` comments are targeted (with specific error codes when supported) and accompanied by a brief explanation if the reason is not obvious.
6. **Check stub files** -- if the project distributes type information via `.pyi` files, verify stubs stay in sync with the implementation signatures.
7. **Respect project conventions** -- if the project does not use type annotations, do not flag their absence. If the project uses a specific typing style, follow it.
