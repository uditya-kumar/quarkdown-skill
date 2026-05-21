# Quarkdown cheatsheet

A one-pager for things you'd otherwise have to re-look-up.

## Document setup boilerplate

```markdown
.docname {Title}
.docauthor {Name}
.doctype {plain|paged|slides|docs}
.doclang {English}
.theme {paperwhite} layout:{latex}
```

Theme combos that work well: `paperwhite+latex` (academic), `darko+minimal` (dark), `galactic+hyperlegible` (web), `beaver+beamer` (slides).

## Function call syntax

```markdown
.fn {pos1} {pos2}                     # positional
.fn {pos1} named:{x}                  # mixed (positional first)
.fn one:{a} two:{b}                   # all named
.a::b                                 # chain: .b {.a}
.a::b {x}                             # chain with extra arg: .b {.a} {x}
H{.text {2} script:{sub}}O            # tight call adjacent to letters
.fn long:{a} \
   args:{b}                           # line continuation

.fn {x}
    body argument as indented block
```

## Inline vs block

- Inline: `Use .fn here in a sentence.`
- Block: stands alone, blank-line separated, can take a body argument.

## Image and figure syntax

```markdown
![Alt](path)                          # base
!(50%)[Alt](path)                     # 50% wide
!(300x200)[Alt](path)                 # explicit
!(5cm*3cm)[Alt](path)                 # use * not x for non-px
!(_ 2in)[Alt](path)                   # auto width
!(50%)[Alt](path "Caption")           # figure with caption
!(50%)[Alt](path "Caption") {#fig-id} # cross-referenceable
```

Width-only: `!(50%)`, `!(300)`, `!(5cm)`, `!(2in)`.

## Math syntax

```markdown
$ inline $                            # spaces required around $
$ block on its own line $              # if isolated
$$$
multi
line
$$$
```

## Headings

```markdown
# H1
## H2
###! Decorative (no number, no TOC)
## My heading {#my-id}

.heading {Custom} depth:{2} numbered:{no} indexed:{no} breakpage:{no}
```

In `paged`, `# H1` triggers an automatic page break. Override with `.autopagebreak maxdepth:{N}` or `.noautopagebreak`.

## Quote types (admonitions)

```markdown
> Note: Some info
> Tip: Helpful tip
> Warning: Be careful
> Important: Critical

> [!NOTE]
> Same effect, GitHub style
```

Or as boxes:

```markdown
.box {Title} type:{tip|note|warning|error|callout}
    body
```

## Footnotes

```markdown
text[^: anonymous footnote]
text[^label: definition]
text[^label]                          # reuse the label

[^name]: standard definition syntax
```

## Cross-references

```markdown
## Section {#section-id}
![Image](path "Caption") {#fig-id}
| ... |
| ... |
"Caption" {#table-id}
$ E = mc^2 $ {#eq-id}
```kotlin {#code-id}
fun main() {}
```

.ref {section-id}
```

## Numbering

```markdown
.numbering
    - headings: 1.1.1
    - figures: 1.1
    - tables: 1.1
    - equations: (1)
    - code: 1
    - footnotes: i
```

Format symbols: `1` decimal, `A`/`a` letters, `I`/`i` Roman. Backslash escapes literal characters. Each symbol corresponds to one nesting level — `1.1.1` numbers up to three levels deep, so a top-level `# Foo` shows as `1`, not `1.1.1`. To see `1.1.1` you need three nested heading levels (`# A` > `## B` > `### C`).

## Auto-replaced text symbols

| Source | Result | Notes |
|---|---|---|
| `--` | — em-dash | |
| `-` | – en-dash | between word + word |
| `...` | … | start/end of word |
| `->` | → | |
| `<-` | ← | |
| `=>` | ⇒ | |
| `<==` | ⇐ | |
| `>=` | ≥ | |
| `<=` | ≤ | |
| `!=` | ≠ | |
| `+-` | ± | |
| `(C)` | © | |
| `(R)` | ® | |
| `(TM)` | ™ | |
| `'word'` | 'word' | smart quotes |
| `"word"` | "word" | smart quotes |

Escape with `\`: `\->` stays as `->`. Replacement does not happen inside code, math, or URLs.

## Sizes

`px`, `pt`, `cm`, `mm`, `in`, `%`. Omit unit → `px`.

`Sizes` (multi-side): `1cm` (all), `2cm 1cm` (vertical horizontal), `2cm 1cm 3cm 2cm` (top right bottom left).

## Colors

`#4290F5`, `rgb(66, 144, 245)`, `rgba(66, 144, 245, 0.8)`, `hsv(214, 73, 96)`, `hsl(214, 90, 61)`, named CSS colors (`blue`, `darkorange`).

## Stack alignment values

- `alignment` (main axis): `start`, `center`, `end`, `spacebetween`, `spacearound`, `spaceevenly`
- `cross` (cross axis): `start`, `center`, `end`, `stretch`

## CLI quick reference

```shell
quarkdown create my-doc           # interactive scaffold
quarkdown c main.qd               # compile to HTML
quarkdown c main.qd --pdf         # PDF (needs Chrome/Chromium)
quarkdown c main.qd -p -w         # live preview + watch
quarkdown c main.qd --strict --clean
quarkdown c main.qd --allow network --allow process
```

Permissions: `project-read` (default), `global-read`, `network`, `native-content` (default), `process`, `all`.

## Common pitfalls

- **Body indentation must be consistent.** Don't mix 2-space with 4-space lines, or tabs with spaces.
- **Blank lines separate items.** `A\nB\nC` is one paragraph (one block); `.row` sees one item.
- **Block calls don't fit in inline `{...}`.** Use the body form.
- **Math `$ ... $` needs spaces** around the `$` symbols.
- **`.currentpage` is a visual node**, not a number — can't be used in arithmetic.
- **Slides treat `#` as a slide break** — use `##` and `##!` for content within a slide.
- **Paged: `#` already auto-page-breaks.** Never put `<<<` immediately before a `#` heading or you get a blank page between chapters.
- **Figures only number with a caption.** `!(...)[Alt](path)` won't be numbered; `!(...)[Alt](path "Caption")` will.
- **Equations only number with a `{#id}`.** Use `{#_}` if you want a number but no real ID.
- **Once you go named, stay named.** Positional args must come before any named args.
