# Tecnologia em comum na suíte FeatherWorks

Tabela de apoio: o que cada aplicativo usa das peças comuns. Levantada dos
`Cargo.toml` e do código em 28/10/2026. Ao mudar uma dependência de algum
app, atualizar a linha dele aqui.

## Interface

| App | Versão | Interface | pelicano | feather-ui |
|---|---|---|---|---|
| FeatherSheet | v0.2.0 | pelicano | v0.1.11 | v0.3.2 (`[patch]` local provisório até a v0.3.3) |
| FeatherSlide | v0.1.0 | Iced 0.13 (`featherslide-gui`) e pelicano (`featherslide-pelicano`), em migração | v0.1.9 | v0.2.10 (Iced) / v0.3.0 (pelicano) |
| FeatherDocs | v0.0.1 | Iced 0.13 (`featherdocs-gui`). **Migrar para o pelicano** | — | v0.2.9 |
| FeatherPDF | 0.0.2 | pelicano (`featherpdf-pelicano`) | v0.1.11 | v0.3.2 |
| FeatherCode | v0.0.1 | pelicano | v0.1.9 | v0.3.0 |
| FeatherNotes | v0.1.8 | pelicano | v0.1.13 | — |
| FeatherWorks Launcher | v0.1.7 | pelicano | v0.1.8 | — |

O `feather-ui` tem duas linhas no mesmo repositório: **v0.2.x = Iced 0.13** e
**v0.3.x = pelicano** (cópia local em `feather-ui-pelicano/`). Código novo vai só
para a v0.3.x.

## Bibliotecas do motor

| Biblioteca | Para quê | Sheet | Slide | Docs | PDF |
|---|---|---|---|---|---|
| `feather-draw` | desenho vetorial, tinta, caneta com pressão | v0.1.1 | v0.1.1 | v0.1.1 / v0.1.3 | v0.1.1 |
| `feather-shapes` | formas (retângulo, seta, balão…) | v0.1.2 | v0.1.2 | — | v0.1.2 |
| `feather-charts` | gráficos | v0.3.2 | v0.3.2 | v0.3.2 | — |
| `feather-io` (`feather-tables`, `feather-image`) | tabelas e imagens comuns, `.xlsx`/`.docx`/`.pptx` | v0.2.0 | v0.1.3 | v0.1.3 | v0.1.3 |
| `feather-macros` (em `feather-io`) | macros em JavaScript; Falcão por padrão desde a v0.2.0 | v0.2.0 | — | — | v0.1.3 |
| `falcao` | motor JavaScript próprio (substitui o Boa) | v0.1.1 | — | — | — |
| `feather-math` | fórmulas matemáticas (OMML, LaTeX) | — | — | dentro do repositório | — |

Crates de fora repetidos em quase todos: `serde`, `zip` (4.2, `deflate`),
`quick-xml` (0.39 no Sheet, 0.42 no Slide e no Docs) e `arrow` 56 (`ipc`), que
guarda o conteúdo dos arquivos `f··z` (`.fshz`, `.fsdz`, `.fdoz`).

## Peças do `feather-ui` em uso

| Módulo | Sheet | Slide | Docs | PDF | Code |
|---|---|---|---|---|---|
| `tema`, `marca`, `icones` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `cromo` (janela, barra de título, menu Arquivo) | ✓ | ✓ | ✓ | — | ✓ |
| `ribbon` (faixa) | ✓ | ✓ | ✓ | ✓ | — |
| `configuracoes` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `ajuda` (F1) | ✓ | ✓ | ✓ | ✓ | — |
| `monitor` (F12) | ✓ | — | ✓ | ✓ | — |
| `dialogo`, `componentes`, `seletor_cor`, `formatacao` | ✓ | ✓ | ✓ | ✓ | — |
| `consulta` (janelas com lateral e ilustração) | ✓ | — | — | — | — |
| `tinta`, `caneta`, `clique_caneta` | ✓ | — | — | — | — |

## Telas de abertura (padrão: FeatherSheet)

As três têm que existir em todos os apps de documento (Sheet, Docs, Slide).

| Tela | Sheet | Docs | Slide | PDF | Code |
|---|---|---|---|---|---|
| Tela inicial (backstage): modelos com miniatura, recentes fixáveis | ✓ `menu_principal.rs` | só lista de recentes | só lista de recentes | `inicio.rs` | `inicio.rs` |
| Primeiros passos (5 passos na primeira abertura) | ✓ `primeiros_passos.rs` | — | — | — | — |
| O que há de novo | ✓ `novidades.rs` | — | — | — | — |

O Sheet monta as três sobre o `feather_ui::consulta` (v0.3.x). Para levar ao
Docs e ao Slide, o caminho é passar o que não é planilha (lateral de passos,
cartões de novidades, galeria de modelos) para o `feather-ui` e deixar no app
só o conteúdo.
