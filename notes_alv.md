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