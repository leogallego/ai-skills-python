---
name: pep8-imports
description: >-
  Use when writing or reviewing Python import statements — ordering, grouping, absolute vs relative, wildcard avoidance, module-level dunder placement. Invoke when user mentions "imports", "import order", "import style", "PEP 8 imports", or asks about Python import organization.
license: MIT
compatibility: Agentskills.io clients (Cursor, Claude Code, Copilot, Gemini). Optional Lola install.
metadata:
  author: Leonardo Gallego
  version: 1.0.0
  collection: pep8
  claude-argument-hint: "[file path or code block]"
  claude-user-invocable: "true"
---

# PEP 8 Imports (Sections 3.7 & 3.8)

Reference: [PEP 8 -- Imports](https://peps.python.org/pep-0008/#imports) and [Module Level Dunder Names](https://peps.python.org/pep-0008/#module-level-dunder-names)

## When to Use

- Writing new Python modules or packages
- Reviewing import statements in existing code
- Reorganizing imports after adding/removing dependencies
- Resolving import-related linting warnings
- Setting up `__all__`, `__version__`, or other module-level dunder names

## Rules

### 1. Separate Lines

Each import statement should be on its own line.

**Correct:**

```python
import os
import sys
```

**Incorrect:**

```python
import os, sys
```

**Exception** -- importing multiple names from a single module is acceptable:

```python
from subprocess import Popen, PIPE
```

### 2. Placement

Imports should be placed at the top of the file, after any module comments and docstrings, and before module globals and constants.

**Correct:**

```python
"""Module for processing data files."""

import os
import json

MAX_RETRIES = 3
```

**Incorrect:**

```python
"""Module for processing data files."""

MAX_RETRIES = 3

import os   # Import after module-level code
import json
```

**Incorrect:**

```python
def process():
    import os  # Import buried inside a function
    return os.getcwd()
```

### 3. Grouping Order

Imports should be grouped in the following order, with a blank line between each group:

1. Standard library imports
2. Related third-party imports
3. Local application/library-specific imports

**Correct:**

```python
import os
import sys

import requests
import flask

from myapp import utils
from myapp.models import User
```

**Incorrect -- no blank lines between groups:**

```python
import os
import sys
import requests
import flask
from myapp import utils
from myapp.models import User
```

**Incorrect -- wrong order:**

```python
from myapp import utils
import os
import requests
```

### 4. Absolute Imports (Recommended)

Absolute imports are recommended because they are more readable and tend to be better behaved when the import system is misconfigured.

**Correct:**

```python
import mypkg.sibling
from mypkg import sibling
from mypkg.sibling import example
```

### 5. Explicit Relative Imports (Acceptable Alternative)

Explicit relative imports are an acceptable alternative to absolute imports, especially when dealing with complex package layouts where using absolute imports would be unnecessarily verbose.

**Correct:**

```python
from . import sibling
from .sibling import example
```

**Incorrect -- implicit relative imports (removed in Python 3):**

```python
import sibling  # Ambiguous: standard library or local?
```

### 6. Class Imports

It is acceptable to import classes directly by name when the class name is unambiguous.

**Correct:**

```python
from myclass import MyClass
from foo.bar.yourclass import YourClass
```

When there is a local name clash, import the module instead to avoid collision:

**Correct -- resolving name clashes:**

```python
import myclass
import foo.bar.yourclass

obj_a = myclass.MyClass()
obj_b = foo.bar.yourclass.YourClass()
```

**Incorrect -- name clash with no resolution:**

```python
from myclass import MyClass
from theirclass import MyClass  # Overwrites the first import
```

### 7. Wildcard Imports

Wildcard imports (`from module import *`) should be avoided. They make it unclear which names are present in the namespace, confusing both readers and automated tools.

**Incorrect:**

```python
from os.path import *
from mymodule import *
```

**Correct -- import only what you need:**

```python
from os.path import join, exists, dirname
from mymodule import HelperClass, utility_function
```

**Exception:** Wildcard imports are acceptable when republishing an internal interface as part of a public API (for example, overwriting a pure Python implementation with an optimized C accelerator, or populating a `__init__.py` that deliberately re-exports a submodule's full interface).

### 8. Module-Level Dunder Names (Section 3.8)

Module-level "dunders" (names with two leading and two trailing underscores, such as `__all__`, `__author__`, `__version__`) should be placed after the module docstring but before any import statements, **except** `from __future__` imports. Python mandates that `__future__` imports appear before any other code besides docstrings and comments.

**Correct -- complete example:**

```python
"""This is the example module.

This module demonstrates proper placement of module-level
dunder names relative to imports.
"""

from __future__ import barry_as_FLUFL

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
```

**Incorrect -- dunders after regular imports:**

```python
"""This is the example module."""

import os
import sys

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
```

**Incorrect -- `__future__` import after regular imports:**

```python
"""This is the example module."""

import os

from __future__ import annotations  # Must come before regular imports
```

## Severity Classification

| Severity    | Rule Violation                                                      |
|-------------|---------------------------------------------------------------------|
| Error       | Imports placed after code (not at the top of the file)              |
| Error       | `__future__` imports not appearing before all other imports         |
| Warning     | Multiple module imports on one line (`import os, sys`)              |
| Warning     | Wrong grouping order (e.g., third-party before stdlib)              |
| Warning     | Missing blank lines between import groups                           |
| Warning     | Wildcard imports (`from module import *`)                           |
| Suggestion  | Using relative imports where absolute imports would be clearer      |
| Suggestion  | Using absolute imports in a deeply nested package where relative imports would reduce verbosity |
| Suggestion  | Module-level dunder names not placed between `__future__` and regular imports |

## Pragmatic Consistency

PEP 8 Section 2 states: "A foolish consistency is the hobgoblin of little minds." When applying these import rules, follow this consistency hierarchy:

1. **Consistency with PEP 8 is important** -- these rules represent the community standard.
2. **Consistency within a project is more important** -- if the project uses `isort` with a specific configuration, or a `pyproject.toml` / `setup.cfg` / `ruff.toml` defines a different import style, follow the project convention.
3. **Consistency within a module is most important** -- never mix styles within a single file.

When reviewing code:

- If the project uses a tool like `isort`, `ruff`, or `black` that enforces its own import ordering, defer to that tool's configuration.
- If the surrounding codebase consistently uses relative imports within a package, continue using relative imports even though PEP 8 recommends absolute.
- Note the PEP 8 recommendation, note the project convention, and recommend following the project convention. Only flag as a warning or error if the code is inconsistent with **both** PEP 8 and the project convention.

## Checklist

When writing or reviewing Python imports, verify each of the following:

- [ ] Each `import` statement is on its own line (no `import os, sys`)
- [ ] All imports are at the top of the file, after docstrings and before module globals
- [ ] `from __future__` imports appear before all other imports
- [ ] Module-level dunders (`__all__`, `__version__`, `__author__`) are placed after `__future__` imports and before regular imports
- [ ] Imports are grouped in the correct order: stdlib, third-party, local
- [ ] A blank line separates each import group
- [ ] No wildcard imports (`from module import *`) unless republishing an internal API
- [ ] Absolute imports are used by default; relative imports are explicit (not implicit)
- [ ] Class imports do not introduce name clashes; module-level import is used when names collide
- [ ] Import style is consistent with the project's existing conventions and tooling (isort, ruff, black)
