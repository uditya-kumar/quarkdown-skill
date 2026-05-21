# Quarkdown function reference

A categorized walkthrough of the standard library. Signatures use `name:{type}` for named/optional arguments and `{type}` for positional. `?` marks optional parameters; `body` is the indented block argument. This is not exhaustive — the live reference is at https://quarkdown.com/docs/quarkdown-stdlib/.

## Table of contents

- [Document setup](#document-setup)
- [Page format and margins](#page-format-and-margins)
- [Layout](#layout)
- [Text formatting](#text-formatting)
- [Headings and structure](#headings-and-structure)
- [Numbering and references](#numbering-and-references)
- [Footnotes](#footnotes)
- [Math and equations](#math-and-equations)
- [Code](#code)
- [Tables](#tables)
- [Charts and diagrams](#charts-and-diagrams)
- [Images and figures](#images-and-figures)
- [Math operations](#math-operations)
- [Logical operations](#logical-operations)
- [String operations](#string-operations)
- [Collections and iterables](#collections-and-iterables)
- [Dictionaries](#dictionaries)
- [Optionality (None)](#optionality-none)
- [Control flow](#control-flow)
- [Variables and scope](#variables-and-scope)
- [File data](#file-data)
- [Localization](#localization)
- [Slides](#slides)
- [HTML and CSS](#html-and-css)
- [Bibliography](#bibliography)
- [Icons and emojis](#icons-and-emojis)
- [Process](#process)

## Document setup

| Function | Purpose |
|---|---|
| `.docname {name}` | Document name. Used in titles, output filenames. |
| `.docauthor {name}` | Single author shorthand. |
| `.docauthors` body: dictionary | Authors with optional metadata (email, etc.). |
| `.docdescription {text}` | Short description, used in HTML meta. |
| `.dockeywords` body: list | SEO keywords. |
| `.doctype {plain|paged|slides|docs}` | Document type. Defaults to `plain`. |
| `.doclang {English}` or `.doclang {en-US}` | Locale: enables localization, hyphenation, locale-specific styling. |
| `.theme {colorTheme}` `layout:{layoutTheme}` | Color: `paperwhite`, `darko`, `galactic`, `beaver`. Layout: `latex`, `hyperlegible`, `minimal`, `beamer`. |
| `.font {family} heading:{family} code:{family} size:{size}` | Font configuration. Family can be a path, URL, system font, or `GoogleFonts:Name`. |

All metadata functions are *modify-or-echo*: calling without arguments returns the previously set value, so you can use `.docname` inline anywhere to reference it.

## Page format and margins

```markdown
.pageformat size:{A4} margin:{2.5cm} orientation:{portrait}
.pageformat size:{letter} columns:{2} alignment:{justify}
.pageformat side:{left} margin:{2cm 3cm 2cm 1cm}    # mirrored margins
.pageformat pages:{2..5} margin:{3cm}                # only pages 2-5
```

| Parameter | Accepts |
|---|---|
| `size` | `A0`–`A10`, `B0`–`B5`, `letter`, `legal`, `ledger` |
| `width`, `height` | Sizes (`px`, `pt`, `cm`, `mm`, `in`, `%`) |
| `orientation` | `portrait`, `landscape` |
| `margin` | One Size, two (`v h`), or four (`top right bottom left`) |
| `bordertop`/`right`/`bottom`/`left`, `bordercolor` | Border around content area |
| `columns` | Integer >= 1; if >= 2, multi-column layout |
| `alignment` | `start`, `center`, `end`, `justify` |
| `side` | `left`, `right` (paged only) |
| `pages` | Range like `2..5` |

```markdown
.pagemargin {position}
    content
```

Positions: `topleftcorner`, `topleft`, `topcenter`, `topright`, `toprightcorner`, `righttop`, `rightmiddle`, `rightbottom`, `bottomrightcorner`, `bottomright`, `bottomcenter`, `bottomleft`, `bottomleftcorner`, `leftbottom`, `leftmiddle`, `lefttop`. Mirror variants: `topoutside`, `topinside`, `bottomoutside`, `bottominside` (and `*corner` versions).

`.footer` is shorthand for `.pagemargin {bottomcenter}`.

```markdown
.pagebreak                  # explicit page break
<<<                         # alternative: 3+ < on a blank line
.autopagebreak maxdepth:{2} # auto-break on H1 and H2
.noautopagebreak            # disable auto page breaks
.landscape body: content    # render content in landscape on a portrait page (experimental)
.fullspan body: content     # span all columns in a multi-column layout
```

```markdown
.currentpage    # visual page number (NOT a number)
.totalpages     # visual total page count
.formatpagenumber {format}     # change format from this page onward (1, A, a, I, i)
.resetpagenumber start:{20}    # reset counter
```

## Layout

```markdown
.row alignment:{center} cross:{center} gap:{1cm}
    item 1

    item 2

.column alignment:{spacearound} gap:{0.5cm}
    item 1

    item 2

.grid columns:{3} alignment:{spacearound} hgap:{1cm} vgap:{0.5cm}
    A

    B

    C

    D
```

Stack params: `alignment` (`start`, `center`, `end`, `spacebetween`, `spacearound`, `spaceevenly`), `cross` (`start`, `center`, `end`, `stretch`), `gap` (Size). Grid additionally requires `columns:{N}` and accepts `hgap`/`vgap` to override `gap`.

```markdown
.container fullwidth:{yes} background:{teal} padding:{1cm} radius:{8px} \
           border:{black} borderwidth:{2px} borderstyle:{dashed} \
           alignment:{center} textalignment:{justify} \
           fontsize:{medium} fontstyle:{italic} fontvariant:{smallcaps} \
           classname:{my-class}
    body content
```

`.container` resets layout context — the canonical wrapper for "this is one item that has internal structure". All styling is optional. Sizes use `px`, `cm`, `mm`, `in`, `pt`, `%`. Colors accept hex, `rgb()`, `rgba()`, `hsv()`, `hsl()`, or named CSS colors.

```markdown
.box {Title} type:{tip}     # callout, tip, note, warning, error
    body content

.center
    centered content

.align {start|center|end}
    content

.whitespace                 # blank line
.whitespace width:{1cm}     # spacer in a row
.whitespace height:{2cm}    # spacer in a column

.float side:{left|right}    # float content to one side
    body content

.clip type:{circle|rounded|...}    # clip child to a shape
    body content
```

## Text formatting

```markdown
.text {content} size:{tiny|small|normal|medium|large|larger|huge} \
                weight:{normal|bold} \
                style:{normal|italic} \
                decoration:{underline|overline|underoverline|strikethrough|all} \
                case:{uppercase|lowercase|capitalize} \
                variant:{normal|smallcaps} \
                script:{sub|sup} \
                url:{https://...} \
                classname:{my-class}
```

Standard Markdown emphasis still works: `**bold**`, `*italic*`, `~~strike~~`, `` `code` ``, `[text](url)`. Quarkdown adds `==highlight==` and the auto-symbol replacements (see cheatsheet).

```markdown
.paragraphstyle alignment:{justify} indent:{2em} lineheight:{1.6} letterspacing:{0.02em}
```

## Headings and structure

```markdown
# H1
## H2
###! Decorative H3 (not numbered, not in TOC)

.heading {My heading} depth:{2} ref:{intro} numbered:{yes} indexed:{yes} breakpage:{yes}

.tableofcontents title:{Contents} maxdepth:{3} focus:{section.id} \
                 headingdepth:{2} numberheading:{no} indexheading:{no} breakpage:{yes}

.marker {chapter-name}      # invisible marker, useful for slides chapter pattern

.subdocumentgraph           # render the knowledge graph (docs doctype)
```

## Numbering and references

```markdown
.numbering
    - headings: 1.1.1
    - figures: 1.1
    - tables: 1.1
    - equations: (1)
    - code: 1
    - footnotes: i

.numbering merge:{no}       # replace, don't merge with previous config
    - figures: 1.1

.nonumbering                # disable all numbering
```

Format symbols: `1` decimal, `A` uppercase letters, `a` lowercase letters, `I` uppercase Roman, `i` lowercase Roman. Backslash escapes: `\1` is a literal `1`. Anything else is treated as a literal character.

```markdown
## Section title {#my-id}
![Logo](logo.png "Caption") {#fig-logo}
$ E = mc^2 $ {#energy}
```kotlin {#code-main}
fun main() {}
```

.ref {my-id}        # cross-reference

.numbered {key} ref:{custom-id}     # custom numbered element
    n:
    Item .n
```

## Footnotes

```markdown
text[^: Anonymous footnote definition]
text[^name: Definition with a label]
text[^name]                          # reference an existing label

[^name]: Standard footnote definition (anywhere in document)
```

## Math and equations

```markdown
$ E = mc^2 $                # inline (whitespace required around $)

The formula:

$ F(u) = \int_0^\infty f(x) dx $    # block (isolated)

$$$
f(x) =
\begin{cases}
    0 & \text{if } x = 0 \\
    1 & \text{otherwise}
\end{cases}
$$$

.texmacro {\R} {\mathbb{R}}     # reusable macro
```

## Code

```markdown
```python
print("hello")
```

.code lang:{python} linenumbers:{no} focus:{5..8} caption:{My snippet}
    .read {snippet.py}

.codespan {dynamic content}     # inline equivalent
```

`.code` evaluates Quarkdown calls inside its body before rendering — useful with `.read` to embed external code.

## Tables

Standard Markdown tables work:

```markdown
| Name | Age |
|------|-----|
| John | 25  |
| Lisa | 32  |
"Caption" {#my-table}
```

Manipulation:

```markdown
.tablesort {2} order:{descending}
    | Name | Age |
    |------|-----|
    | ... |

.tablefilter {2} {@lambda x: .x::isgreater {20}}
    | ... |

.tablecompute {2} {@lambda x: .x::average::round}
    | ... |

.tablecolumn {2}            # extract column as iterable
    | ... |

.tablecolumns               # all columns as iterable of iterables
    | ... |
```

Generation:

```markdown
.table                      # combine tables by columns
    .repeat {3}
        n:
        | Col .n |
        |--------|
        | Cell A |

.tablebyrows {.headers}     # build from rows
    - - John
      - 25
    - - Lisa
      - 32
```

## Charts and diagrams

```markdown
.mermaid caption:{My flowchart}
    flowchart TD
        A[Start] --> B{Choice}
        B -- Yes --> C[End]

.xychart bars:{yes} x:{Months} y:{Revenue} yrange:{100..} caption:{Sales}
    - 250
    - 500
    - 350
```

Mermaid bodies accept Quarkdown function calls. `.xychart` accepts a flat list of values for one line, nested lists for multiple lines, or output of `.tablecolumn` / `.repeat`.

## Images and figures

```markdown
![Alt](path.png)                           # base
!(50%)[Alt](path.png)                      # 50% width, auto height
!(300x200)[Alt](path.png)                  # both dimensions
!(5cm*3cm)[Alt](path.png)                  # cm units (use * not x)
!(_ 2in)[Alt](path.png)                    # auto width, 2in height
!(50%)[Alt](path.png "Caption")            # figure with caption (auto-numbered)
!(50%)[Alt](path.png "Caption") {#fig-id}  # cross-referenceable

.figure caption:{...}                      # explicit figure
    body
```

Width-only shorthands: `!(50%)`, `!(300)`, `!(5cm)`, `!(2in)`. The `_` placeholder means "auto, preserve aspect ratio".

## Math operations

| Function | Effect |
|---|---|
| `.sum {a} {b}` | a + b |
| `.subtract {a} {b}` | a - b |
| `.multiply {a} by:{b}` | a * b |
| `.divide {a} by:{b}` | a / b |
| `.rem {a} {b}` | modulo |
| `.pow {a} to:{b}` | a^b |
| `.abs {x}`, `.negate {x}` | absolute / negate |
| `.sqrt {x}`, `.logn {x}` | square root, natural log |
| `.sin {x}`, `.cos {x}`, `.tan {x}` | trig (radians) |
| `.pi` | π |
| `.truncate {x} {decimals}` | truncate to N decimals |
| `.round {x}` | nearest integer |
| `.iseven {x}`, `.isodd {x}` | even/odd test |
| `.range from:{a} to:{b}` | range value (also `a..b` syntax) |

## Logical operations

| Function | Effect |
|---|---|
| `.if {cond}` body | run body if true |
| `.ifnot {cond}` body | run body if false |
| `.equals {a} to:{b}` | equality |
| `.isgreater {a} than:{b}` `orequals:{yes}` | a > b (or a >= b) |
| `.islower {a} than:{b}` `orequals:{yes}` | a < b (or a <= b) |
| `.not {bool}` | negate |
| `.iseven`, `.isodd`, `.isempty`, `.isnotempty` | various predicates |

## String operations

| Function | Effect |
|---|---|
| `.uppercase {s}`, `.lowercase {s}`, `.capitalize {s}` | case ops |
| `.concatenate {a} with:{b}` `if:{cond}` | string join |
| `.string {value}` | force string conversion |
| `.isempty {s}`, `.isnotempty {s}` | predicates |

## Collections and iterables

Iterables can be Markdown lists, ranges (`1..5`), pairs (`.pair {a} {b}`), or dictionary entries.

| Function | Effect |
|---|---|
| `.foreach {iter}` body: lambda | map each item, returning iterable |
| `.repeat {N}` body: lambda | shorthand for `.foreach {1..N}` |
| `.first from:{iter}`, `.second`, `.third`, `.last` | indexed access |
| `.getat {i} from:{iter} orelse:{default}` | by 1-based index |
| `.size of:{iter}` | length |
| `.sumall from:{iter}` | sum of numeric items |
| `.average from:{iter}` | mean |
| `.distinct from:{iter}` | dedupe |
| `.sorted from:{iter} by:{lambda}` | sort, optional key lambda |
| `.reverse from:{iter}` | reverse order |
| `.group from:{iter} by:{lambda}` | group by key |
| `.takeif from:{iter} {lambda}` | filter |
| `.pair {a} {b}` | create a 2-pair |

Most of these are designed for chained use: `.mylist::sorted::first`.

## Dictionaries

```markdown
.var {data}
    .dictionary
        - key1: value1
        - key2: value2

.foreach {.data}
    key value:
    .key = .value

.get {key1} from:{.data} orelse:{default}
```

`.dictionary` is needed only when the syntax could be ambiguous with a regular Markdown list. Iterating over a dictionary yields key-value pairs, destructurable as shown.

## Optionality (None)

| Function | Effect |
|---|---|
| `.none` | the None value |
| `.x::otherwise {fallback}` | if `.x` is None, use fallback |
| `.x::isnone`, `.x::isnotnone` | predicates |
| `.takeif {predicate}` | returns the value or None |

Pattern: emulate default parameter values in custom functions:

```markdown
.function {greet}
    name?:
    Hello, .name::otherwise {stranger}!
```

## Control flow

```markdown
.if {cond}
    body

.ifnot {cond}
    body

.foreach {iterable}
    item:
    body

.repeat {N}
    body

.let {value}
    name:
    body

.function {name}
    p1 p2 p3?:
    body

.var {name} {value}
.var {name}
    body content as value
```

## Variables and scope

- `.var` is document-wide.
- `.let` is scoped to its lambda.
- Functions and lambdas fork context; child sees parent's declarations, parent does not see child's new declarations.

## File data

```markdown
.read {file.txt}                   # full contents
.read {file.txt} lines:{3..8}      # specific lines
.read {file.txt} lines:{..10}      # first 10 lines
.read {file.txt} lines:{20..}      # from line 20 onward

.csv {data.csv}                    # CSV as table
.csv {data.csv} mode:{markdown}    # cells parsed as Quarkdown
.csv {data.csv} caption:{Sales data}

.listfiles {dir} sortby:{name|date} order:{ascending|descending}
.filename {path/to/file.ext} extension:{no}    # extract file name

.filetree                          # render a directory listing
    - root.qd
    - chapters
      - intro.qd
      - main.qd
```

## Localization

```markdown
.localization
    - English
        - greeting: Hello
        - food: Fish and chips
    - Italian
        - greeting: Ciao
        - food: Pasta

.localize {greeting}            # uses .doclang to pick the right language
```

## Slides

```markdown
.slides center:{yes} controls:{yes} speakernotes:{no} \
        transition:{slide|fade|zoom|none} speed:{default|fast|slow}

.fragment behavior:{show|hide|semihide|showhide}
    revealed content

.speakernotes
    notes for the presenter only
```

## HTML and CSS

```markdown
.css
    body { background: green; }
    h1 { color: pink; }

.css {.read {styles.css}}

.cssproperties
    - background-color: green
    - heading-color: pink
    - block-margin: 12px

.html
    <div class="raw">requires native-content permission</div>
```

Custom classes: `classname:{my-class}` is available on `.container` (block) and `.text` (inline).

## Bibliography

```markdown
.bibliography {refs.bib} style:{ieee|apa|chicago|...}

.cite {key}
.bibliography                  # render the references list
```

## Icons and emojis

```markdown
.icon {github}              # Bootstrap Icons name
.icon {arrow-right}

:smile:                     # standard emoji shortcodes
:rocket:
```

## Process

```markdown
.env {VAR_NAME}             # read environment variable (requires --allow process)
.env {VAR_NAME} orelse:{fallback}
```
