# Shared technology across the FeatherWorks suite

Reference table: which shared pieces each app uses. Collected from the
`Cargo.toml` files and the code on 2026-10-28. When an app changes a
dependency, update its row here.

## User interface

| App | Version | UI toolkit | pelicano | feather-ui |
|---|---|---|---|---|
| FeatherSheet | v0.2.0 | pelicano | v0.1.11 | v0.3.2 (temporary local `[patch]` until v0.3.3) |
| FeatherSlide | v0.1.0 | Iced 0.13 (`featherslide-gui`) and pelicano (`featherslide-pelicano`), migrating | v0.1.9 | v0.2.10 (Iced) / v0.3.0 (pelicano) |
| FeatherDocs | v0.0.1 | Iced 0.13 (`featherdocs-gui`). **To migrate to pelicano** | — | v0.2.9 |
| FeatherPDF | 0.0.2 | pelicano (`featherpdf-pelicano`) | v0.1.11 | v0.3.2 |
| FeatherCode | v0.0.1 | pelicano | v0.1.9 | v0.3.0 |
| FeatherNotes | v0.1.8 | pelicano | v0.1.13 | — |
| FeatherWorks Launcher | v0.1.7 | pelicano | v0.1.8 | — |

`feather-ui` has two lines in the same repository: **v0.2.x = Iced 0.13** and
**v0.3.x = pelicano** (local copy in `feather-ui-pelicano/`). New code goes to
v0.3.x only.

## Engine libraries

| Library | Purpose | Sheet | Slide | Docs | PDF |
|---|---|---|---|---|---|
| `feather-draw` | vector drawing, ink, pen pressure | v0.1.1 | v0.1.1 | v0.1.1 / v0.1.3 | v0.1.1 |
| `feather-shapes` | shapes (rectangle, arrow, callout…) | v0.1.2 | v0.1.2 | — | v0.1.2 |
| `feather-charts` | charts | v0.3.2 | v0.3.2 | v0.3.2 | — |
| `feather-io` (`feather-tables`, `feather-image`) | shared tables and images, `.xlsx`/`.docx`/`.pptx` | v0.2.0 | v0.1.3 | v0.1.3 | v0.1.3 |
| `feather-macros` (in `feather-io`) | JavaScript macros; Falcão by default since v0.2.0 | v0.2.0 | — | — | v0.1.3 |
| `falcao` | in-house JavaScript engine (replaces Boa) | v0.1.1 | — | — | — |
| `feather-math` | math formulas (OMML, LaTeX) | — | — | inside the repository | — |

External crates shared by almost all: `serde`, `zip` (4.2, `deflate`),
`quick-xml` (0.39 in Sheet, 0.42 in Slide and Docs) and `arrow` 56 (`ipc`),
which stores the content of the `f··z` files (`.fshz`, `.fsdz`, `.fdoz`).

## `feather-ui` modules in use

| Module | Sheet | Slide | Docs | PDF | Code |
|---|---|---|---|---|---|
| `tema`, `marca`, `icones` (theme, brand, icons) | ✓ | ✓ | ✓ | ✓ | ✓ |
| `cromo` (window, title bar, File menu) | ✓ | ✓ | ✓ | — | ✓ |
| `ribbon` | ✓ | ✓ | ✓ | ✓ | — |
| `configuracoes` (settings) | ✓ | ✓ | ✓ | ✓ | ✓ |
| `ajuda` (F1 help) | ✓ | ✓ | ✓ | ✓ | — |
| `monitor` (F12) | ✓ | — | ✓ | ✓ | — |
| `dialogo`, `componentes`, `seletor_cor`, `formatacao` | ✓ | ✓ | ✓ | ✓ | — |
| `consulta` (windows with sidebar and illustration) | ✓ | — | — | — | — |
| `tinta`, `caneta`, `clique_caneta` (ink and pen) | ✓ | — | — | — | — |

## Startup screens (reference: FeatherSheet)

All three must exist in every document app (Sheet, Docs, Slide).

| Screen | Sheet | Docs | Slide | PDF | Code |
|---|---|---|---|---|---|
| Start screen (backstage): templates with thumbnails, pinnable recent files | ✓ `menu_principal.rs` | recent list only | recent list only | `inicio.rs` | `inicio.rs` |
| Getting started (5 steps on first launch) | ✓ `primeiros_passos.rs` | — | — | — | — |
| What's new | ✓ `novidades.rs` | — | — | — | — |

Sheet builds all three on top of `feather_ui::consulta` (v0.3.x). To bring them
to Docs and Slide, move the generic parts (step sidebar, what's-new cards,
template gallery) into `feather-ui` and keep only the content in each app.
