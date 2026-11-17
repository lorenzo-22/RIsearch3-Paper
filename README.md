# RIsearch and siOFF — manuscript

LaTeX source for the Application Note manuscript:

> **RIsearch and siOFF: An integrated, high-performance framework for RNA–RNA interaction and siRNA off-target assessment**
> Stefano Roncelli, Lorenzo Favaro, Christian Anthon, Jan Gorodkin
> Center for non-coding RNA in Technology and Health, Section for Health Data Science and AI, Department of Public Health, University of Copenhagen.

This repository holds the paper source only.
The software it describes lives elsewhere: the Rust search core (`risearch`) and the siRNA off-target pipeline (`siOFF`).

## Build

Both PDFs are built with `pdflatex` and `bibtex` from TeX Live (pdfTeX, the engine
that produced the PDFs committed from Overleaf). The built `main.pdf` and `supplementary.pdf` are committed alongside
the sources.

### One-time setup: fontawesome5

`main.tex` uses the `fontawesome5` package for the icons in *Code and data
availability*. Debian ships it only in `texlive-fonts-extra`; if that is installed
(`sudo apt install texlive-fonts-extra`) skip this step and drop the
`\pdfmapfile{...}` prefix below. Without admin rights, install it into your
personal TeX tree from CTAN:

```bash
cd "$(mktemp -d)"
curl -sSLO https://mirrors.ctan.org/fonts/fontawesome5.zip && unzip -q fontawesome5.zip
T="$(kpsewhich -var-value TEXMFHOME)"   # usually ~/texmf
for d in tex/latex fonts/enc/dvips fonts/map/dvips fonts/opentype/public fonts/tfm/public fonts/type1/public; do
  mkdir -p "$T/$d/fontawesome5"
done
cp fontawesome5/tex/*        "$T/tex/latex/fontawesome5/"
cp fontawesome5/enc/*.enc    "$T/fonts/enc/dvips/fontawesome5/"
cp fontawesome5/map/*.map    "$T/fonts/map/dvips/fontawesome5/"
cp fontawesome5/opentype/*   "$T/fonts/opentype/public/fontawesome5/"
cp fontawesome5/tfm/*.tfm    "$T/fonts/tfm/public/fontawesome5/"
cp fontawesome5/type1/*.pfb  "$T/fonts/type1/public/fontawesome5/"
kpsewhich fontawesome5.sty   # should print the installed path
```

A personal install does not register the font map with `updmap`, so the build
command for `main` loads it explicitly with `\pdfmapfile{+fontawesome5.map}`.

### Compile

Build the supplement first: `main.tex` imports its labels from `supplementary.aux`
through `xr`, so Supplementary Figure, Table and Section numbers in the main text
come from the latest supplement build.

```bash
# Supplementary material -> supplementary.pdf, supplementary.aux
pdflatex supplementary
bibtex   supplementary
pdflatex supplementary
pdflatex supplementary

# Main document -> main.pdf
pdflatex -jobname=main '\pdfmapfile{+fontawesome5.map}\input{main}'
bibtex   main
pdflatex -jobname=main '\pdfmapfile{+fontawesome5.map}\input{main}'
pdflatex -jobname=main '\pdfmapfile{+fontawesome5.map}\input{main}'
```

Check the logs for `undefined` references or citations, and the PDFs for `??`
or `[?]`, before committing them.

### Alternative: Tectonic

`mise.toml` pins [Tectonic](https://tectonic-typesetting.github.io), which needs no
system TeX install and fetches the packages it needs:

```bash
mise trust      # authorise mise.toml (first checkout only)
mise install    # fetch the pinned Tectonic
mise run build  # -> supplementary.pdf, then main.pdf
```

Individual targets: `mise run build-supp`, `mise run build-main`, `mise run clean`.
The pinned Tectonic 0.16.9 binary requires glibc 2.39 or newer, so it does not run
on older systems such as Debian 12; use `pdflatex` there.

## Layout

| Path | What |
|------|------|
| `main.tex` | Main document: front matter, body, and back matter |
| `supplementary.tex` | Supplementary material, self-contained (compiled separately) |
| `supplementary.aux` | Committed on purpose: `main.tex` reads the supplement's labels from it (see Overleaf below) |
| `main.pdf`, `supplementary.pdf` | Built manuscript and supplement |
| `reference.bib` | Bibliography |
| `figures/` | Figures as PDF (included) with SVG sources; `summary.tex` is the TikZ source of Figure 1 |
| `oup-authoring-template.cls`, `oup-*.bst` | OUP journal class and BibTeX styles (vendored) |
| `mise.toml` | Pinned Tectonic and build tasks (alternative build) |

## Overleaf and git remotes

The manuscript is co-edited on Overleaf, which is the source of truth; upstream
commits arrive squashed as "Updates from Overleaf". Pull on Overleaf before
editing locally to avoid conflicts. Overleaf's main document is `main.tex` at the
repository root. Overleaf compiles only that one root document, so it relies on the
committed `supplementary.aux` for Supplementary numbering: rebuild the supplement and
commit the updated `.aux` whenever `supplementary.tex` changes. Overleaf ignores
non-`.tex` files, so `mise.toml` is inert there.

The git repository has two remotes:

| Remote | Fetches from | Pushes to |
|--------|--------------|-----------|
| `origin` | GitHub, `lorenzo-22/RIsearch3-Paper` (synced with Overleaf) | GitHub **and** RTH gitolite |
| `rth` | RTH gitolite, `projects/risearch/anthon/risearch3_appnote` | RTH gitolite |

`git push origin main` therefore updates `main` on both servers; on gitolite,
`master` follows `main`. Co-authors can also push directly to gitolite, so before
editing run `git fetch --all` and merge both `origin/main` and `rth/main`.

To set up the double push on a fresh clone:

```bash
git remote set-url --add --push origin git@github.com:lorenzo-22/RIsearch3-Paper.git
git remote set-url --add --push origin gitolite@git.rth.dk:projects/risearch/anthon/risearch3_appnote
git remote add rth gitolite@git.rth.dk:projects/risearch/anthon/risearch3_appnote
```

## License

The OUP authoring template (`oup-authoring-template.cls` and `oup-abbrvnat.bst`) is
© Oxford University Press, released under the LaTeX Project Public License v1.3+.
