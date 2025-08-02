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