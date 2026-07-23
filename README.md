# RIsearch and RIOT — manuscript

LaTeX source for the *Bioinformatics* Application Note:

> **RIsearch and RIOT: An integrated, high-performance framework for RNA–RNA
> interaction and siRNA off-target prediction**
> Stefano Roncelli, Lorenzo Favaro, Christian Anthon, Jan Gorodkin
> Section for Health Data Science and AI, Department of Public Health,
> University of Copenhagen.

This repository holds the paper source only. The software it describes lives
elsewhere: the Rust search core (`risearch`) and the siRNA off-target pipeline
(`RIOT`).

## Build

The build is reproducible with no system TeX install. [mise](https://mise.jdx.dev)
pins the LaTeX engine ([Tectonic](https://tectonic-typesetting.github.io)); Tectonic
fetches exactly the TeX packages the sources request.

```bash
mise trust      # authorise mise.toml (first checkout only)
mise install    # fetch the pinned Tectonic
mise run build  # -> main.pdf and supplementary_main.pdf
```

Individual targets: `mise run build-main`, `mise run build-supp`, `mise run clean`.

With Tectonic already on your `PATH`, you can also build directly:

```bash
tectonic -X compile main.tex
tectonic -X compile supplementary.tex
```

Every push and pull request compiles both PDFs in CI (`.github/workflows/build.yml`),
using the same pinned engine.

## Layout

| Path | What |
|------|------|
| `main.tex` | Main document; inputs `frontmatter`, `body`, `backmatter` |
| `supplementary.tex` | Supplementary material, self-contained (compiled separately) |
| `reference.bib` | Bibliography |
| `figures/` | Figures (PNG) |
| `oup-authoring-template.cls`, `oup-*.bst` | OUP journal class and BibTeX styles (vendored) |
| `mise.toml` | Pinned toolchain and build tasks |

## Overleaf

The manuscript is co-edited on Overleaf, which is the source of truth; upstream
commits arrive squashed as "Updates from Overleaf". Pull on Overleaf before
editing locally to avoid conflicts. Overleaf ignores non-`.tex` files, so `mise.toml`
and the CI workflow are inert there. Overleaf's main document is `main.tex` at the
repository root.

## License

The OUP authoring template (`oup-authoring-template.cls` and `oup-abbrvnat.bst`) is
© Oxford University Press, released under the LaTeX Project Public License v1.3+.
