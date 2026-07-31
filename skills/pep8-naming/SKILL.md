---
name: pep8-naming
description: >-
  Use when writing or reviewing Python naming conventions — modules, classes, functions, variables, constants, type variables, exceptions, public/internal interfaces. Invoke when user mentions "naming", "variable names", "class names", "PEP 8 naming", "snake_case", "CamelCase", or asks about Python naming conventions.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Naming Conventions (Section 8)

Apply these naming rules when writing or reviewing Python code. Every rule references the corresponding PEP 8 section.

---

## Overriding Principle

> Names that are visible to the user as public parts of the API should follow conventions that reflect **usage** rather than **implementation**.

When naming anything that is part of your public interface, choose names that tell the caller *what it does* or *what it represents*, not *how it works internally*.

---

## 1. Names to Avoid (Section 8.3.1)

**Severity: Error**

Never use these single characters as variable names:

| Character | Problem |
|-----------|---------|
| `l` (lowercase L) | Looks like `1` in many fonts |
| `O` (uppercase oh) | Looks like `0` in many fonts |
| `I` (uppercase eye) | Looks like `1` in many fonts |

```python
# WRONG - ambiguous single-character names
l = 1          # Is this L or 1?
O = 2          # Is this O or 0?
I = 3          # Is this I or 1?

# CORRECT - use descriptive names or unambiguous letters
length = 1
offset = 2
index = 3

# Single-character names that ARE acceptable
x = 10         # Clear in context (math, coordinates)
i = 0          # Loop counter (common idiom)
n = 5          # Count (common idiom)
```

---

## 2. Package and Module Names (Section 8.3.3)

**Severity: Warning**

Modules should have short, all-lowercase names. Underscores are acceptable in module names for readability but are discouraged in package names.

C/C++ extension modules that accompany a Python module should use a leading underscore (e.g., `_socket`).

```python
# WRONG - module and package names
import MyModule               # No CapWords for modules
import my_really_long_module_name_that_goes_on_forever  # Keep it short
# Package: My-Package/        # No dashes, no CapWords

# CORRECT - module names
import utils                  # Short, lowercase
import string_utils           # Underscores OK for readability
import _socket                # Leading underscore for C extension modules

# CORRECT - package names
# mypackage/                  # All lowercase, no underscores preferred
# xmlparser/                  # Short, descriptive
```

---

## 3. Class Names (Section 8.3.4)

**Severity: Warning**

Classes should use the CapWords (PascalCase) convention. Keep acronyms fully capitalized.

```python
# WRONG
class my_class:               # snake_case is for functions/variables
    pass

class httpServerError:         # Acronym not capitalized, mixedCase
    pass

class Http_Server_Error:      # Underscores in class names
    pass

# CORRECT
class MyClass:
    pass

class HTTPServerError:         # Acronyms stay ALL CAPS
    pass

class XMLParser:               # Acronym preserved
    pass

class DatabaseConnection:
    pass
```

---

## 4. Type Variable Names (Section 8.3.5)

**Severity: Warning**

Type variables should use CapWords, preferring short names. Use `_co` suffix for covariant and `_contra` suffix for contravariant type variables.

```python
from typing import TypeVar

# CORRECT - basic type variables (short CapWords)
T = TypeVar('T')
AnyStr = TypeVar('AnyStr', str, bytes)
Num = TypeVar('Num', int, float, complex)

# CORRECT - covariant type variables (suffix _co)
T_co = TypeVar('T_co', covariant=True)
VT_co = TypeVar('VT_co', covariant=True)

# CORRECT - contravariant type variables (suffix _contra)
T_contra = TypeVar('T_contra', contravariant=True)
KT_contra = TypeVar('KT_contra', contravariant=True)

# WRONG
t = TypeVar('t')                          # Lowercase
my_type_var = TypeVar('my_type_var')      # snake_case
Tcovariant = TypeVar('Tcovariant', covariant=True)  # Missing _co suffix
```

---

## 5. Exception Names (Section 8.3.6)

**Severity: Warning**

Exceptions are classes, so they follow the CapWords convention. Error exceptions should have the suffix "Error".

```python
# WRONG
class connection_timeout(Exception):      # snake_case
    pass

class BadRequest(Exception):              # Missing "Error" suffix for an error
    pass

# CORRECT
class ConnectionTimeoutError(Exception):
    pass

class BadRequestError(Exception):
    pass

class ValidationError(Exception):
    pass

# Non-error exceptions (warnings, exits) do not need the "Error" suffix
class DeprecationWarning(Warning):        # Warning, not an error
    pass
```

---

## 6. Function and Variable Names (Section 8.3.8)

**Severity: Warning**

Functions and variables should be `lowercase_with_underscores`. Use `mixedCase` only when the prevailing style is already mixedCase (backwards compatibility).

```python
# WRONG
def CalculateTotal(items):          # CapWords is for classes
    ItemCount = len(items)          # CapWords variable
    return ItemCount

def getUser(user_id):               # mixedCase (unless legacy code)
    pass

# CORRECT
def calculate_total(items):
    item_count = len(items)
    return item_count

def get_user(user_id):
    pass

def parse_http_response(response):
    status_code = response.status
    return status_code
```

---

## 7. Method Arguments (Section 8.3.9)

**Severity: Warning**

Always use `self` as the first argument to instance methods and `cls` as the first argument to class methods. When a name conflicts with a Python keyword, append a trailing underscore rather than mangling the spelling.

```python
# WRONG
class MyClass:
    def method(this, value):        # Not "self"
        pass

    @classmethod
    def create(klass, data):        # Not "cls"
        pass

    def set_type(self, clss):       # Mangled spelling
        pass

    def set_class(self, class):     # SyntaxError - keyword without underscore
        pass

# CORRECT
class MyClass:
    def method(self, value):
        pass

    @classmethod
    def create(cls, data):
        pass

    def set_type(self, class_):     # Trailing underscore for keyword conflict
        pass

    def format(self, type_="json"): # Trailing underscore for keyword conflict
        pass
```

---

## 8. Instance Variables and Methods (Section 8.3.10)

**Severity: Warning**

Use `lowercase_with_underscores`. Use a single leading underscore for non-public attributes. Use double leading underscores only to prevent name clashes in subclasses (triggers name mangling).

```python
# WRONG
class Account:
    def __init__(self):
        self.AccountName = "test"        # CapWords
        self._InternalState = None       # CapWords after underscore

# CORRECT
class Account:
    def __init__(self):
        self.account_name = "test"       # Public attribute
        self._internal_state = None      # Non-public (convention)
        self.__conflict_prone = "data"   # Name-mangled to _Account__conflict_prone

    def get_balance(self):               # Public method
        return self._calculate_total()   # Non-public method

    def _calculate_total(self):          # Non-public method
        return 0

    def __init_subclass_data(self):      # Name-mangled (use sparingly)
        pass
```

---

## 9. Constants (Section 8.3.11)

**Severity: Warning**

Constants should be `UPPER_CASE_WITH_UNDERSCORES`, defined at the module level.

```python
# WRONG
maxOverflow = 100          # mixedCase
max_overflow = 100         # Looks like a variable
Total = 1000               # CapWords is for classes
pi = 3.14159               # Lowercase for a constant

# CORRECT
MAX_OVERFLOW = 100
TOTAL = 1000
PI = 3.14159
DEFAULT_TIMEOUT = 30
BASE_API_URL = "https://api.example.com"
MAX_RETRY_COUNT = 3
```

---

## 10. Designing for Inheritance (Section 8.3.12)

**Severity: Suggestion**

Decide whether each attribute is public or non-public. When in doubt, make it non-public -- it is easier to make it public later than to remove a public attribute.

### Rules

- **Public attributes**: no leading underscore. Expose simple data attributes directly; use `@property` for future enhancement without breaking the interface.
- **Double leading underscore**: use only to avoid name conflicts with attributes in classes designed to be subclassed. Do not use it as a general "private" marker.
- **Properties**: keep them side-effect-free and inexpensive. Do not hide computationally expensive operations behind a property.

```python
# CORRECT - designing for inheritance
class BaseProcessor:
    def __init__(self):
        self.name = "default"            # Public: subclasses and callers use this
        self._buffer = []                # Non-public: internal implementation detail
        self.__session_id = None         # Name-mangled: prevents subclass conflicts

    @property
    def buffer_size(self):               # Property: cheap, no side effects
        return len(self._buffer)

    # WRONG - expensive operation hidden behind a property
    # @property
    # def analysis_report(self):
    #     return self._run_full_analysis()  # Too expensive for a property

    def get_analysis_report(self):       # CORRECT - use a method for expensive ops
        return self._run_full_analysis()

    def _run_full_analysis(self):        # Non-public method
        pass
```

---

## 11. Public and Internal Interfaces (Section 8.4)

**Severity: Suggestion**

Use `__all__` to explicitly declare the public API of a module. Any interface not listed in `__all__` should be considered internal. Prefix internal functions, classes, and variables with a single leading underscore.

Imported names are always considered implementation details unless the module is explicitly re-exporting them (documented and listed in `__all__`).

```python
# mypackage/utils.py

__all__ = ["calculate_checksum", "validate_input", "ChecksumError"]

import hashlib              # Implementation detail - not in __all__
from . import _helpers      # Internal module

# Public API
def calculate_checksum(data: bytes) -> str:
    return hashlib.sha256(data).hexdigest()

def validate_input(value: str) -> bool:
    return _is_valid_format(value)

class ChecksumError(Exception):
    pass

# Internal - not in __all__, prefixed with underscore
def _is_valid_format(value: str) -> bool:
    return bool(value and value.strip())

class _InternalCache:
    pass
```

---

## Comprehensive Example

This class demonstrates all naming conventions in one place:

```python
"""user_manager module -- manages user accounts and permissions."""

from typing import TypeVar, Generic

__all__ = ["UserManager", "UserNotFoundError", "DEFAULT_MAX_USERS"]

# Constants (UPPER_CASE_WITH_UNDERSCORES)
DEFAULT_MAX_USERS = 100
SESSION_TIMEOUT_SECONDS = 3600
_INTERNAL_CACHE_SIZE = 256  # Non-public module-level constant

# Type variables (short CapWords, suffixed for variance)
T = TypeVar('T')
UserT_co = TypeVar('UserT_co', covariant=True)


# Exception (CapWords + "Error" suffix)
class UserNotFoundError(Exception):
    """Raised when a requested user does not exist."""
    pass


class PermissionDeniedError(Exception):
    """Raised when an operation is not permitted."""
    pass


# Class (CapWords; acronyms fully capitalized)
class UserManager(Generic[T]):
    """Manage user accounts with role-based access control.

    Public attributes:
        max_users: Maximum number of users allowed.
        name: Human-readable name for this manager instance.

    """

    def __init__(self, name: str, max_users: int = DEFAULT_MAX_USERS):
        # Public attributes (no leading underscore)
        self.name = name
        self.max_users = max_users

        # Non-public attributes (single leading underscore)
        self._users: dict[str, dict] = {}
        self._active_sessions: list[str] = []

        # Name-mangled attribute (double leading underscore)
        # Use only to prevent subclass attribute conflicts
        self.__internal_token = self._generate_token()

    # Public methods (lowercase_with_underscores)
    def add_user(self, user_id: str, role: str = "viewer") -> None:
        """Add a user with the specified role."""
        if len(self._users) >= self.max_users:
            raise PermissionDeniedError("User limit reached")
        self._users[user_id] = {"role": role, "active": True}

    def get_user(self, user_id: str) -> dict:
        """Retrieve user data by ID."""
        if user_id not in self._users:
            raise UserNotFoundError(f"User '{user_id}' not found")
        return self._users[user_id]

    def set_import_(self, import_: str) -> None:
        """Example: trailing underscore avoids keyword conflict."""
        self._last_import = import_

    # Class method (first argument is cls)
    @classmethod
    def from_config(cls, config: dict) -> "UserManager":
        """Create a UserManager from a configuration dictionary."""
        return cls(
            name=config["name"],
            max_users=config.get("max_users", DEFAULT_MAX_USERS),
        )

    # Static method
    @staticmethod
    def validate_user_id(user_id: str) -> bool:
        """Check whether a user ID is well-formed."""
        return bool(user_id and user_id.isalnum())

    # Property (cheap, no side effects)
    @property
    def user_count(self) -> int:
        """Return the current number of users."""
        return len(self._users)

    # Non-public methods (single leading underscore)
    def _generate_token(self) -> str:
        """Generate an internal authentication token."""
        import secrets
        return secrets.token_hex(16)

    def _cleanup_sessions(self) -> None:
        """Remove expired sessions."""
        self._active_sessions.clear()


# Internal helper function (not in __all__, prefixed with underscore)
def _hash_password(password: str) -> str:
    """Hash a password for storage. Internal use only."""
    import hashlib
    return hashlib.sha256(password.encode()).hexdigest()
```

---

## Severity Classification

Use these severity levels when reviewing code for naming violations:

### Error (must fix)
- Using `l` (lowercase L), `O` (uppercase oh), or `I` (uppercase eye) as single-character variable names.

### Warning (should fix)
- Class name not using CapWords convention.
- Function or variable name not using `snake_case`.
- Constant not using `UPPER_CASE_WITH_UNDERSCORES`.
- Missing `self` as first argument in instance methods.
- Missing `cls` as first argument in class methods.
- Non-public attribute or method missing a leading underscore.
- Exception name missing the "Error" suffix (for error exceptions).

### Suggestion (consider fixing)
- Name length: too short to be descriptive or excessively long.
- Use of double-underscore name mangling without a clear subclass-conflict reason.
- Module missing `__all__` to declare its public API.
- Acronym casing inconsistency (e.g., `HttpServer` instead of `HTTPServer`).
- Using `mixedCase` when there is no backwards-compatibility reason.

---

## Pragmatic Consistency

> A foolish consistency is the hobgoblin of little minds. -- PEP 8, quoting Emerson

Break naming rules when:

1. **Consistency within a project**: If the existing codebase uses `mixedCase` throughout, follow that convention rather than introducing `snake_case` in one file.
2. **Backwards compatibility**: Renaming a public API symbol breaks callers. Prefer aliases or deprecation paths over immediate renaming.
3. **Domain conventions**: Scientific and mathematical code may use single uppercase letters (`N`, `M`, `X`) that match the domain's notation, as long as they are not `l`, `O`, or `I`.
4. **Third-party integration**: When wrapping or extending a library that uses different conventions (e.g., `unittest.TestCase.setUp`), follow that library's style for overridden methods.

When you do break a rule, document why in a comment or docstring.

---

## Checklist

Use this checklist when reviewing Python code for naming convention compliance:

- [ ] No use of `l`, `O`, or `I` as single-character variable names.
- [ ] Module and package names are short, all-lowercase (underscores OK in modules).
- [ ] Class names use CapWords with fully capitalized acronyms.
- [ ] Type variables use short CapWords with `_co`/`_contra` suffixes where appropriate.
- [ ] Exception classes use CapWords with "Error" suffix for errors.
- [ ] Functions and variables use `lowercase_with_underscores`.
- [ ] Instance methods use `self` as first argument; class methods use `cls`.
- [ ] Keyword conflicts resolved with trailing underscore (`class_`, not `clss`).
- [ ] Non-public attributes and methods prefixed with single underscore.
- [ ] Double underscore used only for subclass name-conflict prevention.
- [ ] Constants are `UPPER_CASE_WITH_UNDERSCORES` at module level.
- [ ] Properties are cheap and side-effect-free.
- [ ] `__all__` declares the module's public API.
- [ ] Imported names not intended for re-export are treated as internal.
- [ ] Names reflect usage, not implementation.
