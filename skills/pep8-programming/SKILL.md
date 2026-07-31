---
name: pep8-programming
description: >-
  Use when writing or reviewing Python programming patterns — None comparisons, exception handling, return statements, type checking, boolean comparisons, sequence truthiness, string methods, context managers. Invoke when user mentions "PEP 8 programming", "best practices", "Python idioms", "exception handling", "comparisons", or asks about Pythonic code patterns.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Programming Recommendations (Section 9)

These rules cover idiomatic Python patterns: comparisons, exception handling, resource management, type checking, and control flow. Apply them when writing new code or reviewing existing code.

---

## 1. None Comparisons

Always compare to `None` using `is` or `is not`, never equality operators.

```python
# Correct
if foo is not None:
    process(foo)

if result is None:
    raise ValueError("No result")
```

```python
# Wrong
if foo != None:        # uses __eq__, can be overridden
    process(foo)

if not foo is None:    # less readable; use "is not" instead
    raise ValueError("No result")
```

**Caution:** `if x` and `if x is not None` are not interchangeable. Empty containers (`[]`, `{}`, `""`, `0`) are falsy but are not `None`. Use `is not None` when you specifically need to distinguish `None` from other falsy values.

```python
# These behave differently
def process(items=None):
    if items is not None:   # only rejects None
        do_work(items)

    if items:               # also rejects [], "", 0, False
        do_work(items)
```

**Severity: Error** -- Using `==` or `!=` with `None` can produce incorrect results when objects override `__eq__`.

---

## 2. Operator Preference: `is not` over `not ... is`

Both are functionally identical, but `is not` reads more naturally.

```python
# Correct
if foo is not None:
    pass
```

```python
# Wrong
if not foo is None:
    pass
```

**Severity: Suggestion** -- Purely a readability preference, but universally expected.

---

## 3. Rich Comparisons

When defining ordering for objects, implement all six comparison methods or use `functools.total_ordering()` to avoid subtle bugs.

```python
# Correct -- use total_ordering to fill in the rest
import functools

@functools.total_ordering
class Version:
    def __init__(self, major, minor):
        self.major = major
        self.minor = minor

    def __eq__(self, other):
        if not isinstance(other, Version):
            return NotImplemented
        return (self.major, self.minor) == (other.major, other.minor)

    def __lt__(self, other):
        if not isinstance(other, Version):
            return NotImplemented
        return (self.major, self.minor) < (other.major, other.minor)
```

```python
# Correct -- implement all six explicitly
class Score:
    def __init__(self, value):
        self.value = value

    def __eq__(self, other):
        return self.value == other.value

    def __ne__(self, other):
        return self.value != other.value

    def __lt__(self, other):
        return self.value < other.value

    def __le__(self, other):
        return self.value <= other.value

    def __gt__(self, other):
        return self.value > other.value

    def __ge__(self, other):
        return self.value >= other.value
```

```python
# Wrong -- only __lt__ defined; other comparisons may fail or behave unexpectedly
class Score:
    def __init__(self, value):
        self.value = value

    def __lt__(self, other):
        return self.value < other.value
```

**Severity: Warning** -- Missing comparison methods lead to silent failures when sorting or comparing objects.

---

## 4. Lambda vs Def

Never assign a lambda expression to a variable name. Use `def` instead. The only advantage of lambda is embedding anonymous functions inline.

```python
# Correct
def double(x):
    return 2 * x
```

```python
# Wrong
double = lambda x: 2 * x
```

Assigning a lambda to a name defeats its purpose and produces less useful tracebacks (the function shows as `<lambda>` instead of `double`).

Lambda is appropriate inline:

```python
# Correct -- lambda used inline
sorted(items, key=lambda item: item.priority)
```

**Severity: Warning** -- Named lambdas produce unhelpful tracebacks and offer no benefit over `def`.

---

## 5. Exception Derivation

Derive custom exceptions from `Exception`, not `BaseException`. Direct inheritance from `BaseException` is reserved for exceptions that should almost never be caught (e.g., `KeyboardInterrupt`, `SystemExit`).

```python
# Correct
class ValidationError(Exception):
    """Raised when input validation fails."""
    pass

class ServiceUnavailable(Exception):
    """Raised when an external service is unreachable."""
    def __init__(self, service_name, retry_after=None):
        self.service_name = service_name
        self.retry_after = retry_after
        super().__init__(f"Service {service_name} is unavailable")
```

```python
# Wrong -- bare except or except Exception won't catch this
class ValidationError(BaseException):
    pass
```

Design exception hierarchies so that `except` clauses catching the base class handle all related errors. Use descriptive names ending in `Error` for exceptions that represent errors.

**Severity: Warning** -- Exceptions derived from `BaseException` escape `except Exception` handlers and cause unexpected crashes.

---

## 6. Exception Chaining

Use `raise X from Y` to preserve the original cause when re-raising or wrapping exceptions. Use `raise X from None` when you deliberately want to suppress the original context.

```python
# Correct -- explicit chaining preserves the cause
try:
    data = json.loads(raw_input)
except json.JSONDecodeError as exc:
    raise ValidationError("Invalid JSON input") from exc

# Correct -- deliberate suppression
try:
    value = cache.get(key)
except CacheError:
    raise KeyError(key) from None
```

```python
# Wrong -- implicit chaining, unclear relationship
try:
    data = json.loads(raw_input)
except json.JSONDecodeError:
    raise ValidationError("Invalid JSON input")  # confusing __context__
```

**Severity: Warning** -- Without `from`, Python still attaches implicit context, but the relationship between exceptions is unclear to readers.

---

## 7. Specific Exception Catching

Never use bare `except:`. Always catch the most specific exception possible. Bare `except:` catches `SystemExit` and `KeyboardInterrupt`, making the program hard to interrupt and masking real bugs.

```python
# Correct
try:
    import platform_specific_module
except ImportError:
    platform_specific_module = None
```

```python
# Wrong
try:
    import platform_specific_module
except:
    platform_specific_module = None
```

When catching multiple exceptions, use a tuple:

```python
# Correct
try:
    value = int(user_input)
except (ValueError, TypeError) as exc:
    log.warning("Bad input: %s", exc)
    value = default
```

If you must catch broad exceptions (e.g., in a top-level error handler), catch `Exception` and log the full traceback:

```python
# Acceptable -- top-level handler with logging
try:
    main()
except Exception:
    logging.exception("Unhandled error in main loop")
    raise
```

**Severity: Error** -- Bare `except:` silently swallows `KeyboardInterrupt`, `SystemExit`, and `GeneratorExit`, producing extremely hard-to-debug failures.

---

## 8. Try Clause Minimization

Limit the `try` clause to the absolute minimum code necessary. This prevents accidentally catching exceptions from unrelated code. Use the `else` clause for code that should only run when no exception was raised.

```python
# Correct
try:
    value = collection[key]
except KeyError:
    return key_not_found(key)
else:
    return handle_value(value)
```

```python
# Wrong -- a KeyError in handle_value() is incorrectly caught
try:
    return handle_value(collection[key])
except KeyError:
    return key_not_found(key)
```

**Severity: Warning** -- Broad try blocks mask bugs by catching exceptions from code that was never intended to be guarded.

---

## 9. Resource Management with Context Managers

Use `with` statements for resource acquisition and release. They guarantee cleanup even when exceptions occur.

```python
# Correct
with open("data.txt") as f:
    contents = f.read()

with conn.begin_transaction():
    do_stuff_in_transaction(conn)
```

```python
# Wrong -- relies on implicit behavior; unclear what resource is managed
with conn:
    do_stuff_in_transaction(conn)
```

When a context manager does something other than open/close (e.g., transaction management, locking), use an explicit method call that makes the action clear.

For resources that do not support `with` statements, use `try`/`finally`:

```python
# Correct -- explicit cleanup
resource = acquire_resource()
try:
    use_resource(resource)
finally:
    release_resource(resource)
```

**Severity: Suggestion** -- Using explicit context manager methods improves readability and makes resource lifecycle obvious.

---

## 10. Return Consistency

Be consistent in return statements. Either all return statements in a function should return an expression, or none of them should. If any return statement returns an expression, any return statement where no value is returned should explicitly state `return None`, and an explicit return statement should be present at the end of the function.

```python
# Correct -- all returns are explicit
def foo(x):
    if x >= 0:
        return math.sqrt(x)
    else:
        return None
```

```python
# Correct -- no return values
def print_greeting(name):
    if not name:
        return
    print(f"Hello, {name}")
```

```python
# Wrong -- implicit None return
def foo(x):
    if x >= 0:
        return math.sqrt(x)
    # implicitly returns None
```

**Severity: Suggestion** -- Inconsistent returns make it unclear whether `None` is a deliberate return value or a forgotten case.

---

## 11. String Methods over Slicing

Use `str.startswith()` and `str.endswith()` instead of string slicing for prefix/suffix checks. They are cleaner, less error-prone, and support tuples of prefixes/suffixes.

```python
# Correct
if foo.startswith('bar'):
    pass

if filename.endswith(('.tar.gz', '.zip', '.bz2')):
    pass
```

```python
# Wrong
if foo[:3] == 'bar':
    pass

if filename[-7:] == '.tar.gz':
    pass
```

**Severity: Warning** -- Slicing is fragile (off-by-one errors) and does not support checking multiple prefixes/suffixes.

---

## 12. Type Checking with `isinstance()`

Use `isinstance()` instead of comparing types directly. `isinstance()` respects inheritance and is the standard Python idiom.

```python
# Correct
if isinstance(obj, int):
    process_integer(obj)

# Also correct -- checking multiple types
if isinstance(obj, (int, float)):
    process_number(obj)
```

```python
# Wrong
if type(obj) is type(1):
    process_integer(obj)

if type(obj) is int:    # fails for subclasses of int
    process_integer(obj)
```

**Severity: Warning** -- `type()` comparison breaks polymorphism and fails for subclasses.

---

## 13. Sequence Truthiness

Use the fact that empty sequences (`[]`, `()`, `""`, `{}`, `set()`) are falsy. Do not use `len()` to check for emptiness.

```python
# Correct
if not seq:
    print("Sequence is empty")

if seq:
    print("Sequence has items")
```

```python
# Wrong
if len(seq) == 0:
    print("Sequence is empty")

if len(seq):
    print("Sequence has items")

if not len(seq):
    print("Sequence is empty")
```

**Severity: Warning** -- Using `len()` for truthiness is verbose and non-idiomatic. It also fails for objects that implement `__bool__` but not `__len__`.

---

## 14. Boolean Comparisons

Never compare boolean values to `True` or `False` using `==` or `is`.

```python
# Correct
if greeting:
    send_greeting()

if not is_valid:
    raise ValueError("Invalid state")
```

```python
# Wrong
if greeting == True:
    send_greeting()

if greeting is True:     # even worse -- fails for truthy non-bool values
    send_greeting()
```

**Severity: Warning** -- Comparing to `True`/`False` is redundant and, with `is`, breaks for truthy/falsy non-boolean values.

---

## 15. Flow Control in `finally`

Never use `return`, `break`, or `continue` inside a `finally` block. These statements silently cancel any active exception being propagated, causing data loss and masking errors.

```python
# Correct
def safe_divide(a, b):
    try:
        result = a / b
    except ZeroDivisionError:
        return None
    finally:
        log.info("Division attempted")
    return result
```

```python
# Wrong -- the return in finally cancels any ZeroDivisionError
def unsafe_divide(a, b):
    try:
        result = a / b
    except ZeroDivisionError:
        raise ValueError("Cannot divide by zero")
    finally:
        return -1   # silently swallows the ValueError!
```

**Severity: Error** -- Flow control in `finally` silently suppresses exceptions and produces extremely hard-to-diagnose bugs.

---

## 16. String Concatenation in Loops

Use `''.join()` instead of `+=` for building strings in loops. String concatenation with `+=` creates a new string object on each iteration, resulting in quadratic time complexity.

```python
# Correct
parts = []
for item in iterable:
    parts.append(transform(item))
result = ''.join(parts)

# Or use a generator expression
result = ''.join(transform(item) for item in iterable)
```

```python
# Wrong -- O(n^2) for large iterables
result = ''
for item in iterable:
    result += transform(item)
```

**Severity: Warning** -- String concatenation in loops is a common performance anti-pattern.

---

## 17. String Literals and Trailing Whitespace

Do not rely on significant trailing whitespace in string literals. Trailing whitespace is visually indistinguishable and easily stripped by editors, formatters, and version control hooks.

```python
# Correct -- use explicit whitespace representation
message = "Hello, World! " + "More text"
message = "Hello, World! \x20More text"

# Correct -- raw strings or explicit escapes
padding = " " * 4
```

```python
# Wrong -- trailing whitespace may be silently stripped
message = "Hello, World!   "
```

**Severity: Suggestion** -- Trailing whitespace is invisible and fragile.

---

## Severity Classification

| Severity | Rules |
|---|---|
| **Error** | None comparison with `==`/`!=` (Rule 1), bare `except:` (Rule 7), flow control in `finally` (Rule 15) |
| **Warning** | Lambda assigned to variable (Rule 4), exception from `BaseException` (Rule 5), exception chaining omitted (Rule 6), broad try clause (Rule 8), `type()` comparison (Rule 12), `len()` for truthiness (Rule 13), boolean `== True`/`== False` (Rule 14), slice instead of `startswith`/`endswith` (Rule 11), string concatenation in loops (Rule 16) |
| **Suggestion** | Return consistency (Rule 10), context manager style (Rule 9), `is not` vs `not ... is` (Rule 2), trailing whitespace in strings (Rule 17) |

---

## Pragmatic Consistency

Consistency matters more than any single rule. Within a project:

- **Follow the existing style.** If a codebase consistently uses a pattern that differs from these recommendations, match the existing style rather than introducing a mix.
- **Apply rules to new code.** When writing new modules or functions, follow all rules above. When modifying existing code, adopt the surrounding style unless you are specifically refactoring.
- **Readability is the goal, not compliance.** Every rule here exists to make code easier to read, understand, and maintain. If a rule makes a specific piece of code harder to understand, document the exception and move on.
- **Use `# noqa` or equivalent sparingly.** Suppressing a linter rule should require a comment explaining why. The suppression itself is a code smell worth reviewing.

A style guide is about consistency. Consistency within a project is most important. Consistency within a module or function is next. But know when to be inconsistent -- sometimes a rule just does not apply.

---

## Checklist

Use this checklist when writing or reviewing Python code:

- [ ] All `None` comparisons use `is` / `is not`, never `==` / `!=`
- [ ] `is not` is used instead of `not ... is`
- [ ] Classes with ordering implement all six comparisons or use `@total_ordering`
- [ ] No lambdas are assigned to variable names; `def` is used instead
- [ ] Custom exceptions derive from `Exception`, not `BaseException`
- [ ] Re-raised or wrapped exceptions use `raise X from Y`
- [ ] No bare `except:` clauses; exceptions are caught specifically
- [ ] `try` blocks contain only the minimum code that may raise
- [ ] Resources are managed with `with` statements where possible
- [ ] All return paths are explicit and consistent within each function
- [ ] `startswith()` / `endswith()` are used instead of string slicing
- [ ] Type checks use `isinstance()`, not `type()` comparison
- [ ] Empty sequence checks use truthiness, not `len()`
- [ ] Booleans are not compared to `True` or `False`
- [ ] No `return`, `break`, or `continue` in `finally` blocks
- [ ] String building in loops uses `''.join()`, not `+=`
- [ ] No significant trailing whitespace in string literals
