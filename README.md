# Jiahong Sun's knowledge blog

A Quarto website for structured notes on bioinformatics, omics data analysis, machine learning, statistics, Linux, Python, and R.

## Local preview

```bash
quarto preview
```

## Reliable Windows render

Use the project wrapper instead of a bare `quarto render`:

```bash
./render.cmd
```

It uses Quarto's supported `--no-clean` mode and prevents concurrent renders
from modifying the shared `site_libs` files at the same time. This avoids the
intermittent Windows `os error 32` file-lock failure.

After deleting or renaming source pages, perform a clean render so obsolete
HTML files are removed:

```bash
./render.cmd -Clean
```

Do not run `quarto preview` and a full render simultaneously. Stop preview
with `Ctrl+C` before running the render wrapper.

Knowledge templates named `template.qmd` are excluded from the website build.
Copy a template to a descriptive filename before writing a publishable note;
do not rename the template itself or link it from the site navigation.

The rendered site is written to `_site/`. Generated files are ignored by Git;
deployment is configured through GitHub Actions.
