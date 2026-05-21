# Quarkdown cookbook

Complete patterns for the things people actually want to build. Copy, adapt, ship.

## A complete paged document (book / article / report)

```markdown
.docname {Annual Report 2026}
.docauthor {Jane Doe}
.doctype {paged}
.doclang {English}
.theme {paperwhite} layout:{latex}

.pageformat size:{A4} margin:{2.5cm}

.numbering
    - headings: 1.1.1
    - figures: 1.1
    - tables: 1.1
    - equations: (1)

.pagemargin {bottomright}
    .currentpage / .totalpages

.pagemargin {bottomleft}
    .lastheading depth:{1}

.center
    #! .docname

    *.docauthor*

    .currentdate

<<<

.tableofcontents

# Introduction

.loremipsum

# Methodology

.loremipsum

## Data collection {#data}

See .ref {results-table} for the breakdown.

| Region | Revenue |
|--------|---------|
| North  | 1.2M    |
| South  | 0.9M    |
"Annual results by region" {#results-table}

# Conclusions

The .ref {data} section showed...
```

## A complete slides deck

```markdown
.docname {My Talk}
.docauthor {Jane Doe}
.doctype {slides}
.doclang {English}
.theme {beaver} layout:{beamer}

.slides transition:{slide} speed:{fast}

.footer
    .docauthor

    **.docname**

    .currentpage / .totalpages

# Title slide

.center
    #! .docname

    .docauthor

# Outline

.tableofcontents

# Section A

## First slide

- Point one
- Point two

.fragment
    Revealed on next click

## Second slide

Content here.

# Section B

...
```

For chapter-divider slides that show "where we are" in the deck:

```markdown
.function {chapter}
    name:
    .tableofcontents maxdepth:{0} focus:{.name}
    .marker {.name}

.chapter {Section A}
```

## A complete docs (wiki) site

`main.qd`:

```markdown
.docname {My Wiki}
.doctype {docs}
.doclang {English}
.theme {galactic} layout:{hyperlegible}

.subdocumentgraph

- [Getting started](getting-started.qd)
- [User guide](user-guide.qd)
- [API reference](reference.qd)
```

`getting-started.qd`, `user-guide.qd`, `reference.qd` are subdocuments — separate `.qd` files with their own content. Each becomes its own page in the output. Linking between them with Markdown links produces sidebar navigation.

## A complete plain note

```markdown
.docname {Notes on Quarkdown}
.doctype {plain}
.theme {darko} layout:{minimal}

# What is Quarkdown?

A Markdown flavor with functions[^: think LaTeX, but readable].

## Why use it?

Three reasons:

1. **Programmable** with variables, loops, conditionals
2. **Typeset** quality similar to LaTeX
3. **Multi-target** output: HTML, PDF, plain text
```

## Side-by-side columns with mixed content

```markdown
.row alignment:{spacearound} gap:{2cm}
    .container
        ##! Pros
        - Fast
        - Readable
        - Powerful

    .container
        ##! Cons
        - New syntax to learn
        - Smaller ecosystem
```

`.container` is the right wrapper when each item has internal structure (headings, paragraphs, lists). Without it, blocks would either merge or misalign.

## A grid of cards

```markdown
.function {card}
    title body:
    .container background:{#f0f0f0} padding:{1cm} radius:{8px}
        **.title**

        .body

.grid columns:{3} hgap:{1cm} vgap:{1cm}
    .card {Speed}
        Fast compilation.

    .card {Quality}
        LaTeX-grade typography.

    .card {Power}
        Full programming model.
```

## Reusable example/quiz/callout components

```markdown
.function {example}
    title content:
    .box {Example: .title} type:{tip}
        .content

.example {Volume of a sphere}
    The volume is $ V = \frac{4}{3} \pi r^3 $.
```

For lots of these, define a shared library file (`components.qd`) and `.include {components.qd}` at the top of your main file.

## Math derivations with chained math

```markdown
.var {radius} {8}

The surface of a circle of radius **.radius** is
**.pow {.radius} to:{2}::multiply {.pi}::truncate {2}**.
```

`.pow {x} to:{2}::multiply {.pi}::truncate {2}` reads left-to-right as `square it, multiply by π, truncate to 2 decimals` — much easier than the nested form.

## Cross-referenced figures, tables, equations

```markdown
The Quarkdown logo is shown in .ref {logo}.

!(50%)[Logo](logo.png "The Quarkdown logo") {#logo}

Einstein's mass-energy equivalence is .ref {energy}.

$ E = mc^2 $ {#energy}

The data is in .ref {sales-table}.

| Year | Sales |
|------|-------|
| 2024 | 1.2M  |
| 2025 | 1.4M  |
"Sales growth" {#sales-table}
```

## Footnotes — three styles

```markdown
This claim has support[^: inline anonymous footnote].

This claim is well-known[^kuhn].

[^kuhn]: Kuhn, T. (1962). The Structure of Scientific Revolutions.

Two references[^common] to the same[^common] note.

[^common]: Reused footnote.
```

## Tables from CSV, sorted and aggregated

```markdown
.tablecompute {3} {@lambda x: .x::average::round}
    .tablesort {3} order:{descending}
        .csv {data/sales.csv}
```

This sorts the CSV by column 3 descending, then appends an "average" row at the bottom.

## A line chart from CSV

```markdown
.var {cols}
    .tablecolumns
        .csv {data/sales.csv}

.xychart xtags:{.cols::first} x:{Quarter} y:{Sales}
    .cols::second
    .cols::third
```

Single line: pass one iterable. Multiple lines: pass multiple iterables (or a nested list).

## Dynamic tables built from data

```markdown
.var {people}
    - - John
      - 25
      - NYC
    - - Lisa
      - 32
      - LA

.tablebyrows
    - Name
    - Age
    - City

    .people
```

Or generated entirely:

```markdown
.tablebyrows
    .repeat {5}
        y:
        .repeat {3}
            x:
            Cell .x:.y
```

## Mermaid diagrams with caption

```markdown
.mermaid caption:{Login flow}
    flowchart TD
        A[User] --> B{Authenticated?}
        B -- Yes --> C[Dashboard]
        B -- No --> D[Login form]
        D --> A
```

To inject Quarkdown values into a diagram:

```markdown
.var {n1} {2}
.var {n2} {3}

.mermaid
    flowchart TD
        A([Start]) --> B{.n1 + .n2 = ?}
        B -- .sum {.n1} {.n2} --> C([Correct])
```

## Multi-column page layouts

```markdown
.pageformat columns:{2}

# Title

Body text flows in two columns.

.fullspan
    ![Wide image](panorama.jpg)

More body text resumes in two columns.
```

`.fullspan` makes the wrapped content span all columns — useful for hero images, wide tables, or section dividers.

## Mirrored book margins

```markdown
.pageformat side:{left}  margin:{2cm 3cm 2cm 1cm}
.pageformat side:{right} margin:{2cm 1cm 2cm 3cm}
```

Inside margins are larger to leave room for binding.

## Custom numbered elements

```markdown
.numbering
    - headings: 1.1
    - examples: 1.a

.numbered {examples} ref:{ex-pythagoras}
    n:
    **Example .n:** Pythagorean theorem.

    $ a^2 + b^2 = c^2 $

See .ref {ex-pythagoras}.
```

## Splitting a long document across files

`main.qd`:

```markdown
.include {setup.qd}
.tableofcontents

.includeall
    - chapters/01-intro.qd
    - chapters/02-methods.qd
    - chapters/03-results.qd
    - chapters/04-discussion.qd
    - chapters/05-conclusion.qd
```

`setup.qd`:

```markdown
.docname {Thesis}
.docauthor {Jane Doe}
.doctype {paged}
.doclang {English}
.theme {paperwhite} layout:{latex}

.numbering
    - headings: 1.1.1
    - figures: 1.1
    - tables: 1.1

.pagemargin {bottomcenter}
    .currentpage / .totalpages
```

For automatic inclusion of every file in a folder:

```markdown
.includeall {.listfiles {chapters} sortby:{name}}
```

## Theming via CSS overrides

For minor tweaks, prefer `.cssproperties` over raw `.css`:

```markdown
.cssproperties
    - heading-color: "#2a4a8c"
    - block-margin: 14px
    - link-color: darkorange
```

For larger custom styling:

```markdown
.css
    body {
        line-height: 1.7;
    }

    .my-custom-block {
        background: linear-gradient(to right, #4a90e2, #50e3c2);
        padding: 20px;
        border-radius: 12px;
    }
```

Combine with `.container classname:{my-custom-block}` to apply styles to specific blocks.

## Localized boxes and callouts

```markdown
.doclang {Italian}

.box type:{tip}
    Si attiva la traduzione automatica del titolo "Suggerimento".
```

When `.doclang` is set and the locale is supported, omitted box titles, quote prefixes, and figure/table labels become localized automatically.

## Including a code snippet from a file

```markdown
.code lang:{python} caption:{Greeting function}
    .read {snippets/greet.py}
```

To focus on a specific range:

```markdown
.code lang:{python} focus:{12..20}
    .read {snippets/large_module.py}
```

## A printable cover page

```markdown
.center
    #! .docname

    .text {.docauthor} size:{large}

    .whitespace height:{4cm}

    .text {.text {Department of Physics} variant:{smallcaps}}

    .text {Spring 2026} size:{small}

.pagebreak
```

## Bibliography (BibTeX-backed)

```markdown
.bibliography {refs.bib} style:{ieee}

According to .cite {einstein1905}, the speed of light is constant.

# References

.bibliography
```

The first `.bibliography {file}` call loads the source. The second, parameterless call renders the references list.

## Reading environment variables

```markdown
# Build .env {BUILD_NUMBER}

Generated on .env {BUILD_DATE} orelse:{unknown date}.
```

Requires `--allow process` when compiling.

## Conditional sections

```markdown
.var {draft} {yes}

.if {.draft}
    > [!WARNING]
    > This is a draft. Do not distribute.

# Real content here
```

A common pattern is to gate experimental content behind a flag, then flip it once for "release".
