<p align="center"><img src="https://raw.githubusercontent.com/go-synctex/brand/main/social/go-synctex.png" alt="go-synctex" width="640"></p>

<h1 align="center">go-synctex</h1>
<p align="center"><strong>TeX's SyncTeX (source ↔ PDF) in pure Go — no cgo, zero dependencies.</strong></p>

<p align="center">
  🌐 <a href="https://go-synctex.github.io">Website</a> ·
  📚 <a href="https://go-synctex.github.io/docs/">Documentation</a>
</p>

<p align="center">
  <a href="https://go-synctex.github.io/docs/"><img alt="Docs" src="https://img.shields.io/badge/docs-mkdocs--material-4F46E5?style=flat-square"></a>
  <a href="https://github.com/go-synctex/synctex/blob/main/LICENSE"><img alt="License: BSD-3-Clause" src="https://img.shields.io/badge/license-BSD--3--Clause-blue?style=flat-square"></a>
  <img alt="Go 1.26.4+" src="https://img.shields.io/badge/go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white">
  <img alt="Coverage 100%" src="https://img.shields.io/badge/coverage-100%25-1a7f37?style=flat-square">
</p>

---

go-synctex is a **pure-Go (no cgo) parser for [SyncTeX](https://www.tug.org/TUGboat/tb29-3/tb93laurens.pdf)**,
the `.synctex.gz` file TeX engines (`pdflatex` / `xelatex` / `lualatex`) emit to
link a `.tex` **source line** to the exact **spot on the PDF page** — in both
directions.

It answers the two queries an editor or PDF viewer needs:

- **Forward** — `(source file, line)` → `(page, x, y)`: jump the viewer to where
  a source line was typeset.
- **Backward** — `(page, x, y)` → `(source file, line)`: click a glyph in the PDF
  and jump back to the source that produced it.

Zero external dependencies — the standard library only. It cross-compiles and
embeds anywhere, guards against hostile input (a decompression-size cap defeats
zip-bomb `.synctex.gz` files), and can scope `Input:` paths to a project root so
absolute host paths never leak back to a client.

## Repositories

| Repo | What it is |
|------|------------|
| [**synctex**](https://github.com/go-synctex/synctex) | the parser: `Parse`, `Forward` / `Backward`, `ResolveSource`, `WithProjectRoot`, and the `Record` model |
| [**docs**](https://github.com/go-synctex/docs) | MkDocs Material documentation, versioned with [mike], served at [/docs/](https://go-synctex.github.io/docs/) |
| [**go-synctex.github.io**](https://github.com/go-synctex/go-synctex.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-synctex/brand) | logos and brand assets |

## Principles

- **Pure Go, zero cgo.** Cross-compiles and embeds anywhere; a static binary by
  default. Nothing to install alongside your editor or viewer.
- **Both directions.** Forward (source → PDF) and backward (PDF → source), the
  full round-trip an editor/viewer integration needs.
- **Hardened.** A decompression-size cap defeats zip-bomb `.synctex.gz`; an
  optional project-root scope drops out-of-tree `Input:` paths and rewrites the
  rest relative-to-root.
- **Standalone & reusable.** No dependency beyond the standard library.
- **100% test coverage** — including every error and malformed-record branch —
  enforced as a CI gate, green across the six 64-bit Go targets.

## Status

**Parser complete.** Reads `.synctex.gz` (and plain `.synctex`), indexes the
records, and answers forward `(file, line) → (page, x, y)` and backward
`(page, x, y) → (file, line)` queries, plus `ResolveSource` for editor-relative
paths and `WithProjectRoot` path sanitisation. Coordinates are exposed as raw
SyncTeX scaled points (sp) so callers apply their own point/DPI scaling. 100%
coverage, `gofmt` + `go vet` clean, CI green across the six 64-bit Go targets
(amd64, arm64, riscv64, loong64, ppc64le, s390x).

BSD-3-Clause.

[mike]: https://github.com/jimporter/mike
