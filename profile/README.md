<p align="center">
  <img src="imagens/featherworks.svg" width="96" alt="FeatherWorks">
</p>

<h1 align="center">FeatherWorks</h1>

<p align="center">
  A family of lightweight, native and private office apps, 100% Rust, on our own UI toolkit (<a href="https://github.com/feather-works/pelicano">pelicano</a>).
</p>

## Start with the launcher

The **FeatherWorks Launcher** installs, updates and opens every app in the suite. It picks the right package for your system (Linux, Windows or macOS) and checks each one against its SHA256 before installing.

<p align="center">
  <a href="https://github.com/feather-works/featherlauncher/releases/latest/download/featherlauncher-linux-amd64.deb"><b>⬇ Linux (.deb)</b></a>
  &nbsp;·&nbsp;
  <a href="https://github.com/feather-works/featherlauncher/releases/latest/download/featherlauncher-linux-x86_64.tar.gz"><b>⬇ Linux (.tar.gz)</b></a>
  &nbsp;·&nbsp;
  <a href="https://github.com/feather-works/featherlauncher/releases/latest/download/featherlauncher-windows-x64.exe"><b>⬇ Windows (.exe)</b></a>
</p>

<p align="center">
  <a href="https://github.com/feather-works/featherlauncher">
    <img src="imagens/launcher.png" width="820" alt="FeatherWorks Launcher with FeatherSheet, FeatherSlide and FeatherCode installed">
  </a>
</p>

For the `.tar.gz`, extract it and run `./instalar.sh`; the Windows `.exe` is portable, just open it.

## Private by default

No account, no login, no telemetry. Everything runs on your machine and none of your content leaves it.

## The apps

| | App | What for | Status | Standalone install |
|---|---|---|---|---|
| <img src="imagens/feathersheet.svg" width="32"> | [**FeatherSheet**](https://github.com/feather-works/feathersheet) | Spreadsheets: formulas in Portuguese and English, millions of rows, charts, pivot tables, OpenQuery, `.xlsx` and Excel VBA macros converted to JavaScript | v0.2.0 | [Linux, Windows and macOS](https://github.com/feather-works/feathersheet/releases/latest) |
| <img src="imagens/featherslide.svg" width="32"> | [**FeatherSlide**](https://github.com/feather-works/featherslide) | Presentations: opens `.pptx` and `.odp`, exports PDF | v0.1.0 | [Linux and Windows](https://github.com/feather-works/featherslide/releases/latest) |
| <img src="imagens/feathercode.svg" width="32"> | [**FeatherCode**](https://github.com/feather-works/feathercode) | Code editor with Claude Code in the side panel | v0.0.2 | [Linux and Windows](https://github.com/feather-works/feathercode/releases/latest) |
| | [**FeatherNotes**](https://github.com/feather-works/feathernotes) | Notepad and desktop sticky notes: tabs that come back, Markdown, 30+ encodings | v0.1.7 | [Linux and Windows](https://github.com/feather-works/feathernotes/releases/latest) |
| <img src="imagens/featherdocs.svg" width="32"> | [**FeatherDocs**](https://github.com/feather-works/featherdocs) | Documents: DOCX, LaTeX and Markdown | in development | — |
| <img src="imagens/featherpdf.svg" width="32"> | **FeatherPDF** | Reading and organizing PDFs: annotations, forms, OCR | in development | — |
| <img src="imagens/feathercad.svg" width="32"> | **FeatherCAD** | 2D and 3D technical drawing | in development | — |

Each app is an independent product with its own repository, version and installer. If you prefer to install without the launcher, each repository's releases page has the packages (`.deb`, `.tar.gz`, `.exe`) and the `SHA256SUMS`.

## The `f··z` files

```
f   sh   z      → .fshz  (FeatherSheet)
f   do   z      → .fdoz  (FeatherDocs)
f   sd   z      → .fsdz  (FeatherSlide)
│   │    └── z: compressed (ZIP or gzip)
│   └─────── the file type
└─────────── Feather
```

The content is stored in **Apache Arrow**, a binary columnar format, with no JSON inside.

### `.fsdz` (presentation, ZIP)

```
apresentacao.fsdz
├── data/
│   ├── apresentacao.arrow   ← 1 row: title, author, aspect ratio, format version
│   ├── slides.arrow         ← 1 row per slide: layout, background, notes, hidden, comments
│   ├── elementos.arrow      ← 1 row per element: slide, position, colors, text, style,
│   │                          link, table/chart ("dados" column)
│   ├── personalizadas.arrow ← custom slide shows
│   └── fontes.arrow         ← which fonts are embedded
└── assets/
    ├── imagem-1.png         ← images as real files (not base64)
    └── fontes/
        └── Roboto-regular.ttf  ← embedded fonts
```

### `.fdoz` (document, ZIP)

```
documento.fdoz
├── data/
│   ├── documento.arrow      ← 1 row: title, author, paper, margins, header, footer
│   ├── blocos.arrow         ← 1 row per paragraph, heading, list, table…
│   ├── trechos.arrow        ← the formatted text (bold, color…), pointing to its block
│   ├── tabelas.arrow        ← the shape of each table (rows, columns, merges)
│   ├── estilos.arrow        ← named styles (Heading 1, Quote…)
│   ├── marcadores.arrow     ← bookmarks and cross-references
│   ├── comentarios.arrow    ← review comments
│   ├── respostas.arrow      ← replies to comments
│   └── fontes.arrow         ← embedded fonts
└── assets/
    └── imagem-1.png
```

### `.fshz` (spreadsheet, FSH3 binary)

A spreadsheet can have millions of rows, so this format is a single binary stream instead of many files inside an archive:

```
planilha.fshz
├── "FSH3"                         ← signature (4 bytes)
└── gzip ──────────────────────────────────────────────
    ├── metadata size              ← u32
    ├── metadata (MessagePack)
    │   ├── version, active sheet, defined names, styles, string table
    │   └── one entry per sheet:
    │       name, tab color, hidden, column widths and row heights,
    │       charts, tables, pivot tables, OpenQuery, validation,
    │       merges, images, shapes, ink, buttons, comments,
    │       conditional formatting, cell/row/column styles,
    │       formulas (one pattern per column), loose cells
    └── Apache Arrow IPC blocks    ← cell values, column by column
```

### What comes next

The format contract reserves an **embedded Git repository** in each file, to keep version history without a server:

```
arquivo.f··z
├── manifest.json
├── .git/        ← not implemented yet
├── data/
└── assets/
```

### Why it beats `.xlsx`, `.docx` and `.pptx`

| | Office (`.xlsx` / `.docx` / `.pptx`) | Feather (`.f··z`) |
|---|---|---|
| Content | ZIP of XML: marked-up text | Apache Arrow: binary, columnar |
| Numbers | turned into text and parsed again on open | stay numbers, no conversion |
| Speed | reading large XML is slow | a 2-million-row spreadsheet opens in about 1 s (measured) |
| Images and fonts | files inside the ZIP | files inside the ZIP (inside the binary for `.fshz`) |
| Saving | no guarantee if it crashes midway | writes aside, then replaces (atomic) |
| Reading with other tools | needs an Office library | Arrow opens directly in pandas, Polars and DuckDB |
| Privacy | account and company metadata | no account, no telemetry |
| Compatibility | — | the apps still open and save `.xlsx`, `.docx`, `.pptx` and `.pdf` |

## Shared libraries

| Repository | What it does |
|---|---|
| [pelicano](https://github.com/feather-works/pelicano) | The suite's UI toolkit in Rust: windows, widgets, translucent glass, layout inspector |
| [feather-ui](https://github.com/feather-works/feather-ui) | Theme, icons, brand and shared components (ribbon, settings, F1 help, F12 monitor) |
| [falcao](https://github.com/feather-works/falcao) | JavaScript engine in Rust with no dependencies; runs the macros |
| [feather-draw](https://github.com/feather-works/feather-draw) | Geometry, paths, SVG, PDF and pressure-sensitive ink |
| [feather-charts](https://github.com/feather-works/feather-charts) | Charts: model, types, axes and rendering |
| [feather-shapes](https://github.com/feather-works/feather-shapes) | Shapes, icons and SmartArt |
| [feather-io](https://github.com/feather-works/feather-io) | Files, tables, images and macros: formats and conversion |

Which app uses which piece, and at which version: [TECHNOLOGY.md](../TECHNOLOGY.md).

## Status

FeatherSheet, FeatherSlide, FeatherCode and FeatherNotes have published releases; the other apps are in development and join the launcher as they ship.
