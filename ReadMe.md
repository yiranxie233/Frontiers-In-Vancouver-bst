# Frontiers Vancouver BibTeX Style: Locally Modified `@article` Layout

English | [简体中文](ReadMe.zh-CN.md)

## Overview

This repository keeps the Frontiers-provided style as an unmodified comparison baseline and provides a locally modified variant for journal-article references. The variant was created to reproduce the journal-article layout observed by the repository author on the Frontiers website, which differed from the baseline output.

For a typical `@article` containing `journal`, `year`, `volume`, and an `eid` or `pages` locator, the modified style targets this fragment:

```text
Journal. (Year) Volume:Locator.
```

Here, `Locator` is the article number from `eid`, or the page range from `pages` when `eid` is absent.

The modified file is not an official Frontiers release. Publisher instructions and templates can change, so compare the rendered bibliography with the current requirements of the target Frontiers journal before submission.

## Files

| File | Purpose |
| --- | --- |
| [`Frontiers-Vancouver.bst`](Frontiers-Vancouver.bst) | Frontiers-provided, unmodified baseline. |
| [`Frontiers-Vancouver (1).bst`](<Frontiers-Vancouver (1).bst>) | Locally modified variant described in this document. |

## What Changed

The active changes are limited to `@article` entries:

- a period is added after the journal name;
- the parenthesized year is placed before the volume;
- the volume is printed in regular weight instead of bold;
- the volume and locator are joined with a colon, with no surrounding spaces;
- `eid` is used when present; otherwise the style falls back to `pages`;
- page ranges still pass through the style's dash normalization.

The following examples show only the journal/year/volume/locator part of an entry:

| Input | Frontiers-provided baseline | Modified variant |
| --- | --- | --- |
| `eid = {e12345}` | *Journal* **12** (2025) e12345 | *Journal*. (2025) 12:e12345 |
| `pages = {101--109}` | *Journal* **12** (2025) 101–109 | *Journal*. (2025) 12:101–109 |

Author formatting, titles, DOI handling, citation labels, sorting, and non-`@article` entry types are inherited unchanged from the baseline style.

## Use in LaTeX

This is a classic BibTeX `.bst` style, not a `biblatex`/Biber style.

### 1. Prepare the style file

The modified file's current name contains a space and parentheses, which can be unreliable across TeX/BibTeX toolchains. Make a working copy with a simple name, while retaining the two repository files for comparison:

```text
Frontiers-Vancouver (1).bst  ->  Frontiers-Vancouver-modified.bst
```

Place `Frontiers-Vancouver-modified.bst` in the same directory as the main `.tex` file. This also works on Overleaf: upload the file to the project root and give it the same simple name.

### 2. Select the style

A minimal standalone example is:

```tex
\documentclass{article}

% Omit this line if the document class or Frontiers template already loads natbib.
\usepackage[numbers,sort&compress]{natbib}

\begin{document}

An example citation~\cite{smith2025}.

% Use the style file's basename; do not include the .bst extension.
\bibliographystyle{Frontiers-Vancouver-modified}
% Use the bibliography database's basename; do not include the .bib extension.
\bibliography{references}

\end{document}
```

If the Frontiers template already loads and configures `natbib`, do not load it a second time; keep the template's citation configuration and add only the two bibliography commands near the end of the document.

### 3. Prepare the BibTeX data

For an article number, use the `eid` field:

```bibtex
@article{smith2025,
  author  = {Smith, Jane and Doe, John},
  title   = {An Example Article},
  journal = {Frontiers in Example},
  year    = {2025},
  volume  = {12},
  eid     = {e12345},
  doi     = {10.1234/example.12345}
}
```

For a conventional page range, omit `eid` and use:

```bibtex
pages = {101--109},
```

If both `eid` and `pages` are present, the modified style uses `eid`. Enter a bare DOI such as `10.1234/example.12345`; the generated `.bbl` calls `\doi{...}` and provides a `doi:` fallback when that command is otherwise undefined. The style italicizes the supplied `journal` value but does not abbreviate it automatically, so use the name or abbreviation required by the target journal.

### 4. Compile

Run BibTeX between LaTeX passes:

```console
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

The argument to `bibtex` is the main document's job name, not the `.bib` filename. XeLaTeX or LuaLaTeX can replace `pdflatex`, but use the same engine for all LaTeX passes. With `latexmk`, the equivalent command is usually:

```console
latexmk -pdf -bibtex main.tex
```

Overleaf normally detects and runs BibTeX automatically. After changing styles, use **Recompile from scratch** if an old `.bbl` is still being shown.

## Using the Unmodified Baseline

To use the Frontiers-provided file instead, keep `Frontiers-Vancouver.bst` beside the main document and select:

```tex
\bibliographystyle{Frontiers-Vancouver}
```

Run BibTeX again whenever the selected style changes.

## Scope and Limitations

- This patch changes the bibliography layout of `@article` entries only.
- It does not change citation labels, bibliography sorting, author formatting, DOI handling, or other entry types.
- The underlying style uses BibTeX and natbib-compatible labels. Numeric in-text rendering is controlled by the Frontiers template or natbib options, not by this patch.
- It is not loaded with `\usepackage[style=...]{biblatex}`, `\addbibresource`, `\printbibliography`, or Biber.
- The baseline file's header describes it as intended for English texts and for Frontiers journals in the health domain or *Frontiers in Physics*; check its suitability for other journals.
- No style can guarantee acceptance; always check the generated PDF against the target journal's current author instructions.

## License and Provenance

The headers of both `.bst` files state that the style was generated from `merlin.mbs`/`physjour.mbs`, is copyrighted by Patrick W. Daly, and may be redistributed and/or modified under the LaTeX Project Public License, version 1 or later. Preserve the copyright and license notices when redistributing a modified copy.
