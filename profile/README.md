<p align="center">
  <img src="https://raw.githubusercontent.com/FeatherOffice/feather-ui/main/assets/logos/pena.svg" width="96" alt="FeatherOffice">
</p>

<h1 align="center">FeatherOffice</h1>

<p align="center">
  Uma família de aplicativos de escritório leves, nativos e privados, feitos 100% em Rust com Iced.
</p>

---

## Privacidade por padrão

Sem conta, sem login, sem telemetria. Tudo roda na sua máquina e nada do seu conteúdo sai dela.

## Os aplicativos

| | Aplicativo | Para quê | Formato |
|---|---|---|---|
| <img src="https://raw.githubusercontent.com/FeatherOffice/feather-ui/main/assets/logos/feathersheet.svg" width="32"> | **FeatherSheet** | Planilhas: fórmulas em português e inglês, gráficos, tabelas dinâmicas, macros, abre e grava `.xlsx` | `.fshz` |
| <img src="https://raw.githubusercontent.com/FeatherOffice/feather-ui/main/assets/logos/featherdocs.svg" width="32"> | **FeatherDocs** | Textos: DOCX, LaTeX e Markdown | `.fdoz` |
| <img src="https://raw.githubusercontent.com/FeatherOffice/feather-ui/main/assets/logos/featherslide.svg" width="32"> | **FeatherSlide** | Apresentações: abre `.pptx` e `.odp` | `.fsdz` |

Cada aplicativo é um produto independente, com repositório, versão e instalador próprios. O instalador geral vai permitir escolher só os que você quer.

## Os arquivos `f··z`

```
f   sh   z      → .fshz  (FeatherSheet)
f   do   z      → .fdoz  (FeatherDocs)
f   sd   z      → .fsdz  (FeatherSlide)
│   │    └── z: compactado (ZIP ou gzip)
│   └─────── o tipo do arquivo
└─────────── Feather
```

O conteúdo fica em **Apache Arrow**, um formato binário por colunas, sem JSON dentro.

### `.fsdz` (apresentação, ZIP)

```
apresentacao.fsdz
├── data/
│   ├── apresentacao.arrow   ← 1 linha: título, autor, proporção, versão do formato
│   ├── slides.arrow         ← 1 linha por slide: layout, fundo, notas, oculto, comentários
│   ├── elementos.arrow      ← 1 linha por elemento: slide, posição, cores, texto, estilo,
│   │                          link, tabela/gráfico (coluna "dados")
│   ├── personalizadas.arrow ← sequências de slides personalizadas
│   └── fontes.arrow         ← quais fontes estão embutidas
└── assets/
    ├── imagem-1.png         ← imagens como arquivo de verdade (não base64)
    └── fontes/
        └── Roboto-regular.ttf  ← fontes embutidas
```

### `.fdoz` (documento, ZIP)

```
documento.fdoz
├── data/
│   ├── documento.arrow      ← 1 linha: título, autor, papel, margens, cabeçalho, rodapé
│   ├── blocos.arrow         ← 1 linha por parágrafo, título, lista, tabela…
│   ├── trechos.arrow        ← o texto formatado (negrito, cor…), apontando o bloco
│   ├── tabelas.arrow        ← a forma de cada tabela (linhas, colunas, mesclas)
│   ├── estilos.arrow        ← estilos com nome (Título 1, Citação…)
│   ├── marcadores.arrow     ← indicadores e referências cruzadas
│   ├── comentarios.arrow    ← comentários da revisão
│   ├── respostas.arrow      ← respostas aos comentários
│   └── fontes.arrow         ← fontes embutidas
└── assets/
    └── imagem-1.png
```

### `.fshz` (planilha, binário FSH3)

Planilha pode ter milhões de linhas. Por isso esse formato é um fluxo binário único, sem vários arquivos dentro:

```
planilha.fshz
├── "FSH3"                         ← assinatura (4 bytes)
└── gzip ──────────────────────────────────────────────
    ├── tamanho dos metadados      ← u32
    ├── metadados (MessagePack)
    │   ├── versão, aba ativa, nomes definidos, estilos, tabela de textos
    │   └── uma entrada por aba:
    │       nome, cor da aba, oculta, larguras e alturas,
    │       gráficos, tabelas, tabelas dinâmicas, OpenQuery, validação,
    │       mesclas, imagens, formas, tinta, botões, comentários,
    │       formatação condicional, estilos por célula/linha/coluna,
    │       fórmulas (padrão por coluna), células avulsas
    └── blocos Apache Arrow IPC    ← os valores das células, coluna por coluna
```

### O que vem depois

O contrato do formato prevê um repositório **Git embutido** em cada arquivo, para guardar o histórico de versões sem servidor:

```
arquivo.f··z
├── manifest.json
├── .git/        ← ainda não implementado
├── data/
└── assets/
```

### Por que é melhor que `.xlsx`, `.docx` e `.pptx`

| | Office (`.xlsx` / `.docx` / `.pptx`) | Feather (`.f··z`) |
|---|---|---|
| Conteúdo | ZIP de XML: texto com marcação | Apache Arrow: binário por colunas |
| Números | viram texto e são convertidos de novo ao abrir | ficam como número, sem conversão |
| Velocidade | ler XML grande é lento | planilha de 2 milhões de linhas abre em cerca de 1 s (medido) |
| Imagens e fontes | arquivos dentro do ZIP | arquivos dentro do ZIP (no `.fshz`, dentro do binário) |
| Gravação | sem garantia se cair no meio | grava à parte e só então substitui (atômica) |
| Ler com outras ferramentas | precisa de biblioteca de Office | Arrow abre direto em pandas, Polars e DuckDB |
| Privacidade | metadados de conta e empresa | nada de conta nem telemetria |
| Compatibilidade | — | os apps continuam abrindo e salvando `.xlsx`, `.docx`, `.pptx` e `.pdf` |

## Bibliotecas compartilhadas

| Repositório | O que faz |
|---|---|
| [feather-ui](https://github.com/FeatherOffice/feather-ui) | Tema, ícones, marca e componentes Iced |
| [feather-draw](https://github.com/FeatherOffice/feather-draw) | Geometria, caminhos, SVG, PDF e tinta com pressão |
| [feather-charts](https://github.com/FeatherOffice/feather-charts) | Gráficos: modelo, tipos, eixos e desenho |
| [feather-shapes](https://github.com/FeatherOffice/feather-shapes) | Formas, ícones e SmartArt |

## Estado

Em desenvolvimento. A primeira versão alfa de cada aplicativo está prevista para **30/10/2026**.
