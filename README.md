[![Build Status](https://travis-ci.org/asottile/reorder_python_imports.svg?branch=master)](https://travis-ci.org/asottile/reorder_python_imports)
[![Coverage Status](https://img.shields.io/coveralls/asottile/reorder_python_imports.svg?branch=master)](https://coveralls.io/r/asottile/reorder_python_imports)

reorder_python_imports
==========

Tool for automatically reordering python imports.  Similar to `isort` but
uses static analysis more.


## Installation

`pip install reorder-python-imports`


## Console scripts

```
reorder-python-imports --help
usage: reorder-python-imports [-h] [--diff-only] [filenames [filenames ...]]

positional arguments:
  filenames

optional arguments:
  -h, --help   show this help message and exit
  --diff-only  Show unified diff instead of applying reordering.
  --add-import ADD_IMPORT
                        Import to add to each file. Can be specified multiple
                        times.
  --remove-import REMOVE_IMPORT
                        Import to remove from each file. Can be specified
                        multiple times.
```

## As a pre-commit hook

See [pre-commit](https://github.com/pre-commit/pre-commit) for instructions

Hooks available:
- `reorder-python-imports` - This hook reorders imports in python files.


## What does it do?

### Separates imports into three sections

```
import sys
import pyramid
import reorder_python_imports
```

becomes

```
import sys

import pyramid

import reorder_python_imports
```

### `import` imports before `from` imports

```
from os import path
import sys
```

becomes

```
import sys
from os import path
```

### Splits `from` imports (may be configurable in the future!)

```
from os.path import abspath, exists
```

becomes

```
from os.path import abspath
from os.path import exists
```

### Using `# noreorder`

Lines containing and after lines which contain a `# noreorder` comment will
be ignored.  Additionally any imports that appear after non-whitespace
non-comment lines will be ignored.

For instance, these will not be changed:

```
import sys

try:  # not import, not whitespace
    import foo
except ImportError:
    pass
```


```
import sys

import reorder_python_imports

import matplotlib  # noreorder
matplotlib.use('Agg')
import matplotlib.pyplot as plt
```

```
# noreorder
import sys
import pyramid
import reorder_python_imports
```

## Adding / Removing Imports

Let's say I want to enforce `absolute_import` across my codebase.  I can use: `--add-import 'from __future__ import absolute_import'`.

```
$ cat test.py
print('Hello world')
$ reorder-python-imports --add-import 'from __future__ import absolute_import' test.py
Reordering imports in test.py
$ cat test.py
from __future__ import absolute_import
print('Hello world')
```

Let's say I no longer care about supporting Python 2.5, I can remove `from __future__ import with_statement` with `--remove-import 'from __future__ import with_statement'`

```
$ cat test.py
from __future__ import with_statement
with open('foo.txt', 'w') as foo_f:
    foo_f.write('hello world')
$ reorder-python-imports --remove-import 'from __future__ import with_statement' test.py
Reordering imports in test.py
$ cat test.py
with open('foo.txt', 'w') as foo_f:
    foo_f.write('hello world')
```
