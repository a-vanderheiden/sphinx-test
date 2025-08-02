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