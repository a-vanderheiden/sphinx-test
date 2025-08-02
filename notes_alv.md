# Notes

## Project structure
All files relevant for sphinx should be in the `docs/` directory

```
docs
├── build            # contains all rendered docs w/ a directory for each output type
│   ├── doctrees
│   ├── epub
│   └── html
├── make.bat         # Convenience scripts to build docs (windows)
├── Makefile         # Convenience scripts to build docs (mac/linux)
└── source           # contains all source material to be rendered and other config files; this is tracked in git 
    ├── _static
    ├── _templates
    ├── conf.py      # config file for sphinx (docs version, extensions, etc)
    └── index.rst    # root document; contains the root table of contents (toctree)
```

## How to build docs

run `sphinx-build -M html <source_dir> <output_dir>` or `make html` depending on if you have the convenience scripts 

## Themes

Themes are installed with pip ex. `pip install furo`, then you set the theme in conf.py
```python
html_theme = "furo"
```

## Adding multiple pages

To add another page to your project, you need to add it to the `toctree` directive in index.rst.

This allows the page to show up as a link on the root page.

If there is a doc file in docs/source that's not listed in the `toctree`,  users wont be able to access the doc and you'll get the following error message in the build phase:

> WARNING: document isn't included in any toctree

Pages can be cross referenced within the project as well with the `:doc:` role

Also, you can reference arbitrary locations by inserting a label a document ex: `.. _installation` then referencing the label with a `:ref:` role

See [index.rst](docs/source/index.rst) for examples.

## Python directives in Sphinx

Sphinx can render docs for many languages with directives and roles organized in **domains** for each language.

For example `.. py:function` references the function directive in the python domain.

All such directives can be cross-referenced in the project with **:py:func:`function_name`**

## Referencing python code directly

In order to reference the code directly, sphinx needs to be able to import the code in [conf.py](docs/source/conf.py). This can be done one of two ways:
1. ammend `sys.path` in the  with the following
    ```python
    # If extensions (or modules to document with autodoc) are in another directory,
    # add these directories to sys.path here.
    import sys
    from pathlib import Path
    sys.path.insert(0, str(Path(__file__).resolve().parents[2]))
    ``` 
2. create a `pyproject.toml` file to make the code installable
    ```python
    import lumache
    ```

Once the code is imported you can use extensions like doctest to import and test code snippets in the docs.

## Automatically generating docs from code

In addition to doctest, you can use autodoc to automatically update the documented python objects and reuse your docstrings.

This is done by applying the [sphinx formatting](https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html) your docstrings then replacing the `py:___` domain directives with their corresponding `auto____` directives to import directly from python modules. 

Example: 

From this...
```
.. py:function:: lumache.get_random_ingredients(kind=None)

   Return a list of random ingredients as strings.

   :param kind: Optional "kind" of ingredients.
   :type kind: list[str] or None
   :raise lumache.InvalidKindError: If the kind is invalid.
   :return: The ingredients list.
   :rtype: list[str]
```
... to this:
```
.. autofunction:: lumache.get_random_ingredients    # where this function has the preformatted docstring reused by autodoc
```

## Extensions

### doctest
Import and test snippets of your code in the docs.

Syntax: 
- precede the lines of python you want doctest to run with `>>>`
- provide the expected output on the following line

Example (from [usage.rst](docs/source/usage.rst)):
```python
>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']
```

To actually run the tests, execute
```
make doctest
```

Errors look like:
```
Document: usage
---------------
**********************************************************************
File "usage.rst", line 41, in default
Failed example:
    lumache.get_random_ingredients()
Expected:
    ['shells', 'gorgonzola', 'parsley']
Got:
    ['eggs', 'bacon', 'spam']
**********************************************************************
1 items had failures:
   1 of   2 in default
2 tests in 1 items.
1 passed and 1 failed.
***Test Failed*** 1 failures.

Doctest summary
===============
    2 tests
    1 failure in tests
    0 failures in setup code
    0 failures in cleanup code
build finished with problems.
make: *** [doctest] Error 1
```

Success looks like:

```
Document: usage
---------------
1 items passed all tests:
   2 tests in default
2 tests in 1 items.
2 passed and 0 failed.
Test passed.

Doctest summary
===============
    2 tests
    0 failures in tests
    0 failures in setup code
    0 failures in cleanup code
build succeeded.

Testing of doctests in the sources finished, look at the results in build/doctest/output.txt.
```

### autodoc
Replace manual references to your code in the docs with auto imports *directly* from your code.

Syntax:
- format your py docstrings in the [sphinx style](https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html)
    - see [lumache.py](lumache.py)
- replace the `py:___` directive references to your python object in the docs with `auto____` directives 
    - ex `.. autofunction:: lumache.get_random_ingredients`