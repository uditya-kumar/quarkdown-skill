---
name: quarkdown
description: Author Quarkdown (.qd) source files for typeset documents — books, articles, reports, presentations, wikis, and PDFs. Use this skill whenever the user wants to write or edit a Quarkdown document, asks about Quarkdown syntax (functions starting with a dot like `.doctype`, `.box`, `.row`, `.foreach`), needs help producing slides/paged/plain/docs output, wants a programmable Markdown alternative to LaTeX with high-quality typography, asks how to build PDFs from Markdown-like sources, or mentions `.qd` files. Prefer this over plain Markdown whenever the user is working in a `.qd` file or has Quarkdown installed.
---

# Quarkdown

Quarkdown is a Turing-complete Markdown flavor and typesetting system. Sources are `.qd` files. The compiler produces HTML, PDF (via Puppeteer), or plain text. Think "Markdown that can do what LaTeX does", with first-class functions, variables, loops, and conditionals.

This skill is the field manual: how to think in Quarkdown, the function call syntax that everything else builds on, the patterns that produce good documents, and the traps that produce broken ones.

## When to reach for which feature

Always start by deciding the document type — it changes layout, page behavior, footnote rendering, and which functions even apply.

| User wants… | Use `.doctype` | Notes |
|---|---|---|
| A note, blog post, README-like page | `plain` | Continuous flow, no page breaks. Sidenote-style footnotes. |
| A book, article, report, thesis, paper | `paged` | Page breaks, page numbers, default LaTeX-like numbering. |
| A presentation, lecture, talk | `slides` | One slide per H1, fragments, transitions. |
| A wiki, knowledge base, technical docs | `docs` | Sidebar nav, search, multiple subdocument files. |

Then pick a theme combination. Defaults are fine; explicit common picks: `paperwhite + latex` (academic), `darko + minimal` (dark, clean), `galactic + hyperlegible` (highly readable web), `beaver + beamer` (academic slides).

## The one syntax rule that explains everything

Every function call starts with a `.` and uses curly braces for arguments. Once you internalize this, the rest is vocabulary.

```markdown
.functionname {arg1} named:{arg2}
```

Calls come in two flavors that look almost identical but behave very differently:

- **Inline** — sits inside a paragraph, output is spliced in. `Hello, .uppercase {world}!`
- **Block** — sits alone on its own line(s) and can take an indented **body argument**:

  ```markdown
  .center
      This whole indented block
      becomes the body.
  ```

The body argument is **always the last parameter** of the function. Indentation must be at least 2 spaces or 1 tab and consistent across all body lines — inconsistent indentation silently turns part of the body into a code block.

Three more syntactic moves you will use constantly:

1. **Chaining with `::`** — `.a::b` is sugar for `.b {.a}`. Reads naturally for math and pipelines: `.sqrt {10}::round::multiply {2}`.
2. **Tight calls with `{...}`** — wrapping a whole call in braces lets it sit next to word characters: `H{.text {2} script:{sub}}O`.
3. **Line continuation with `\`** — split long calls across lines: `.container width:{100} \` ... newline ... `padding:{1cm}`.

For body arguments, prefer the indented block form whenever possible — it is much more readable than nested inline braces.

Read `references/syntax.md` when something doesn't parse the way you expect. The most common cause is body indentation or a paragraph that didn't get separated by a blank line.

## How to write a Quarkdown document

A complete document has roughly this shape. Put metadata first, content second.

```markdown
.docname {My Report}
.docauthor {Jane Doe}
.doctype {paged}
.doclang {English}
.theme {paperwhite} layout:{latex}

.pageformat size:{A4} margin:{2.5cm}

.numbering
    - headings: 1.1.1
    - figures: 1.1
    - tables: 1.1

.pagemargin {bottomcenter}
    .currentpage / .totalpages

<!-- Visible cover page: required if the user expects to SEE a title -->
.center
    #! .docname

    *.docauthor*

<<<

.tableofcontents

# Introduction

The first chapter of the report...

# Methods

...
```

Two things in the boilerplate that matter and aren't obvious:

- The `.center` block with `#! .docname` is what actually puts a visible title on the page. `.docname` alone only sets metadata. `#!` is a decorative heading — it renders large but doesn't enter the TOC or the heading-numbering sequence, which is what you want for a cover.
- `<<<` is an explicit page break, so the cover gets its own page before the TOC. Without it, the title and the TOC end up on page 1 together.

A few things worth knowing up front, because they catch people:

- **`.doctype` defaults to `plain`** — if the user wants a PDF book or a slide deck, set it explicitly.
- **`.docname` and `.docauthor` only set metadata.** They do *not* render a visible title or byline anywhere in the output. If the user asks for a "report titled X by Y", or otherwise expects the title to appear on the page, you must build a visible title block yourself — typically a centered cover page above the table of contents. See the title-block recipe in the cookbook (and the `paged.qd` template). Skipping this is the single most common way to ship a "completed" report that has no title on it.
- **Heading numbering format strings count nesting levels, not heading levels.** `.numbering` with `headings: 1.1.1` means *up to three nested levels* will be numbered as `1`, `1.1`, `1.1.1`. A document with only top-level `#` chapters will show `1`, `2`, `3`, not `1.1.1`. To get a `1.1.1` rendering on a single heading, that heading needs to be three levels deep (`### Foo` under `## Bar` under `# Baz`).
- **Figures, tables, equations and code only get numbered if they have a caption / cross-ref ID.** A bare `![Logo](logo.png)` is not numbered; `![Logo](logo.png "The logo")` is.
- **`#` triggers an automatic page break in `paged` documents** by default. Use `.autopagebreak maxdepth:{N}` or `.noautopagebreak` to change this. Use `##!` (decorative heading) to suppress numbering and TOC entry for a single heading.
- **Never put `<<<` immediately before a `#` heading in a `paged` document.** The `#` already auto-breaks; the explicit `<<<` adds a *second* break, which renders as a blank page between every chapter. Reserve `<<<` for forcing a break in places where one wouldn't otherwise happen — e.g., between a cover (`.center` block) and the TOC, or between two `##` sections you specifically want on separate pages. If a chapter starts with `#`, it already gets its own page; do not add `<<<` above it.
- **Markdown lazy lines bite hard.** `A`, `B`, `C` on three consecutive lines without blank lines between them is *one paragraph*, not three list items. Stack functions like `.row` see one block, not three. Always separate items with blank lines, or use `.container` to force a fresh layout context.
- **Writing in code blocks is exempt from text symbol replacement.** Outside code, `--` becomes `—`, `->` becomes `→`, `(C)` becomes `©`, etc. See `references/cheatsheet.md`.

## The cookbook

When the user asks for something concrete, jump to the matching pattern. These are the moves that come up over and over:

| Need | Reach for |
|---|---|
| Side-by-side columns of content | `.row` (flat) or `.grid columns:{N}`. Wrap each column in `.container` if it has its own headings/text. |
| Vertically stacked items with control | `.column` |
| Centered title or hero text | `.center` (shorthand for `.align {center}`) |
| Callout / admonition box | `.box {Title} type:{tip|note|warning|error|callout}` or a typed quote `> Note: ...` |
| Variable or constant value | `.var {name} {value}`, then `.name` to use it |
| Reusable component | `.function {name}` ... `param1 param2:` ... body |
| Loop over items | `.foreach {iterable}` ... `item:` ... body. `.repeat {N}` for plain counts. |
| Conditional content | `.if {condition}` / `.ifnot {condition}` |
| Math expression inline | `$ E = mc^2 $` (whitespace around `$` is required) |
| Multi-line math block | `$$$` ... `$$$` |
| Numbered equation | `$ E = mc^2 $ {#energy}` then `.ref {energy}` to cite |
| Image with size | `!(50%)[Alt](path.png)` or `!(300x200)[Alt](path.png)` |
| Figure with caption | `!(50%)[Logo](logo.png "Caption text")` |
| Code block with line numbers | Standard fenced \`\`\` block, or `.code lang:{python}` for dynamic content |
| Code from file | `.code lang:{python}` body: `.read {snippet.py}` |
| Mermaid diagram | `.mermaid` body: mermaid source. Add `caption:{...}` to number it. |
| 2D chart | `.xychart x:{...} y:{...}` body: list of values |
| CSV as a table | `.csv {data.csv}` (add `mode:{markdown}` to allow rich cells) |
| Table of contents | `.tableofcontents maxdepth:{2}` |
| Page counter on every page | `.pagemargin {bottomcenter}` body: `.currentpage / .totalpages` |
| Visible title / cover page | `.center` body: large title (`#! .docname`) + author (`.docauthor`) + date, then `<<<` for an explicit page break before the TOC |
| Slide fragment (reveal piece by piece) | `.fragment` body: content, optional `behavior:{show|hide|semihide|showhide}` |
| Cross-reference | Add `{#id}` to target, then use `.ref {id}` to cite |
| Footnote (compact) | `text[^: definition]` or labeled `[^name: definition]` then later `[^name]` |
| Split a big document | `.include {chapter1.qd}` or `.includeall {.listfiles {chapters}}` |
| Wide content on a portrait page | `.landscape` body: content (experimental) |
| Multi-column page | `.pageformat columns:{2}`, with `.fullspan` for content that spans all columns |
| Custom font | `.font {family-or-path}` (supports `GoogleFonts:Name`, system names, file paths, URLs) |

For deeper recipes — author dictionaries, scoped page formatting, bibliography, subdocuments, persistent headings, custom numbered elements — see `references/cookbook.md`.

For the comprehensive function list with signatures (math, logical, string, collection, dictionary, optional, layout, document, slides, html), see `references/functions.md`.

## How to actually compile and view it

The user will usually have the `quarkdown` CLI on PATH after installation.

```shell
quarkdown create my-doc          # interactive project wizard
quarkdown c main.qd              # compile to HTML in ./quarkdown-output/
quarkdown c main.qd --pdf        # compile to PDF (needs Chrome/Chromium for Puppeteer)
quarkdown c main.qd -p -w        # live preview + watch mode
quarkdown c main.qd --strict --clean   # fail on first error, wipe output dir first
```

Common flags: `-o <dir>` output directory, `--render text` for plain text, `--allow network` if the document fetches remote assets, `--allow global-read` to read files outside the project, `--allow process` to read environment variables. Default permissions are `project-read` and `native-content`.

When writing for a user, do not assume their project layout. If they have a `main.qd`, leave includes alone. If they're starting fresh, suggest `quarkdown create` rather than hand-writing the boilerplate.

## What to avoid

These are the mistakes that produce mysterious behavior. Catch them before the user does.

- **Don't stack `<<<` before a `#` heading in `paged` mode.** `#` already auto-page-breaks, so `<<<` followed by `#` produces a blank page. If you want a chapter on a fresh page, just start it with `#` — nothing else.
- **Don't put `.row` items on consecutive lines without blank lines.** They merge into one paragraph (one item from the row's perspective). Always blank-line separate, or use `.container` for each item that has internal structure.
- **Don't use `.center { ... .row ... }` with a row inside braces.** Inline arguments don't accept block calls. Use the body form: `.center` newline indented `.row` ...
- **Don't expect `.currentpage` to be a number.** `.currentpage` and `.totalpages` are visual nodes, not values — you cannot do `.sum {.currentpage} {3}`.
- **Don't mix tabs and spaces in body arguments.** Pick one and be consistent inside a single body block.
- **Don't forget to escape literal dots that start a line** if they would be parsed as a function call. Use a backslash: `\.something`.
- **Don't expect inline `$math$`** to work without spaces around the `$`. `Let $x$ be...` is wrong. `Let $ x $ be...` is right.
- **Don't write `.doctype slides` and then use `#` for slide content** without realizing each `#` becomes a new slide. Use `##` or `##!` for headings inside a slide.
- **Don't use named arguments that come *before* positional ones.** Once you switch to named, all following must be named.

## When the user is stuck

Check the obvious things first:

1. Is `.doctype` set, and matches what they're trying to do? (Page-counter + `plain` won't work.)
2. Did they forget the blank line between blocks inside a body argument?
3. Did they nest a block call inside `{...}` instead of using the indented body?
4. Is `.numbering` set if they expect figures/tables/equations to have numbers?
5. Did they capture an image's caption with `"..."` so it becomes a numbered figure?
6. Did permission issues block file/network access? (`--allow ...`)

Then look at `references/cookbook.md` for a pattern that matches their goal, or `references/functions.md` for the exact signature of the function that's misbehaving.

## Quick orientation files

- `references/syntax.md` — full syntax rules: positional vs named args, body args, chaining, lambdas, destructuring, line continuation, tight calls, escaping
- `references/functions.md` — categorized reference of stdlib functions with signatures and one-line summaries
- `references/cookbook.md` — longer recipes: layouts, custom components, slides, paged docs, docs sites, tables, charts, bibliographies, subdocuments, themes
- `references/cheatsheet.md` — terse one-pager: text symbols, image syntax, footnote syntax, quote types, headings, common gotchas
- `assets/templates/` — ready-to-edit starter templates for each `.doctype`
