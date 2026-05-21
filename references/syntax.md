# Quarkdown syntax reference

Everything you need to write correct Quarkdown function calls and avoid the parser surprises.

## Function call basics

```markdown
.functionname {arg1} {arg2}              # positional
.functionname firstparam:{a} secondparam:{b}    # named
.functionname {arg1} secondparam:{b}     # mixed (positional first, then named)
```

Once you switch to named arguments, every argument that follows must also be named. You cannot put a positional after a named.

## Inline vs block

A function call is **inline** when it sits inside other inline content (a paragraph, a sentence, a heading):

```markdown
The square of 5 is .pow {5} {2}.
```

A function call is **block** when it stands alone on its own line(s), separated from surrounding content by blank lines:

```markdown
First paragraph.

.center
    My centered content

Second paragraph.
```

The crucial difference: only block calls can take a **body argument**, the indented multi-line block that follows the call. Inline calls cannot.

## The body argument

The body argument is **always the last parameter** of the function — even if you used named arguments for everything else. It is not wrapped in braces; it is provided as an indented block:

```markdown
.box {My title} type:{tip}
    This is the body of the box.
    It can span multiple lines.
```

Rules:
- Indent every body line with at least 2 spaces or 1 tab.
- Use the **same** indentation across all lines of a single body. Mixing 4-space and 2-space lines, or tabs and spaces, will misparse — usually turning some content into a code block.
- Blank lines inside a body are allowed and separate paragraphs.
- A body can contain other block function calls — they must be indented relative to the parent call.

```markdown
.row alignment:{center} gap:{1cm}
    First column

    Second column

    .column                    # nested block
        Stacked A

        Stacked B
```

## The "single block per item" rule (and why blank lines matter)

Stack functions like `.row`, `.column`, `.grid`, and any function that takes "items" via its body, see Markdown blocks. Three lines of text without blank lines between them are *one* paragraph block:

```markdown
.row gap:{1cm}
    A
    B
    C
```

Above produces a row with **one** item containing the text "A B C". To get three items:

```markdown
.row gap:{1cm}
    A

    B

    C
```

If an item has its own internal structure (a heading + paragraph, multiple paragraphs), wrap it in `.container` to reset the layout context:

```markdown
.row gap:{1cm}
    .container
        ##! Left
        Text on the left.

    .container
        ##! Right
        Text on the right.
```

## Nesting calls in arguments

Function calls nest freely inside `{...}` arguments:

```markdown
.multiply {.pow {3} to:{2}} by:{.pi}
```

Multi-line arguments are fine — indentation is arbitrary inside `{...}`:

```markdown
.divide {
    .cos {.pi}
} by:{
    .sum {2} {1}
}
```

But **block** calls (with body arguments) cannot live inside inline `{...}` arguments. This is wrong:

```markdown
.center {
    .row
        Hi
}
```

Use the body form instead:

```markdown
.center
    .row
        Hi
```

## Chaining with `::`

`.a::b` is sugar for `.b {.a}`. The chained value becomes the **first positional argument** of the next call.

```markdown
.sqrt {10}::round::multiply {2}
# expands to:
.multiply {.round {.sqrt {10}}} {2}
```

You can append more arguments to any link in the chain:

```markdown
.sum {10} {5}::multiply {2}
# = .multiply {.sum {10} {5}} {2}
```

Reads naturally for math and string operations:

```markdown
.var {name} {hello}
.name::uppercase::concatenate with:{!}
```

## Tight calls

A function call must normally be surrounded by whitespace, punctuation, or line boundaries. To place a call directly adjacent to word characters, wrap the entire call in `{...}`:

```markdown
H{.text {2} script:{sub}}O
```

The wrapping braces are consumed and don't appear in output.

## Line continuation

Long calls split across lines with a trailing backslash:

```markdown
.container alignment:{center} \
           background:{red} \
           padding:{1px}
```

The backslash plus newline plus leading whitespace on the next line are all consumed. Works in both inline and block calls.

## Lambdas

Many functions take lambdas as their body. The header (parameter list) goes on its own line, ending with `:`. The body follows:

```markdown
.foreach {1..3}
    n:
    Number is .n
```

Implicit positional parameters with `.1`, `.2`, ... are available without declaring a header:

```markdown
.foreach {1..3}
    The number is .1
```

For inline lambdas (those passed in `{...}`), prefix with `@lambda` so the parser knows what it is:

```markdown
.tablefilter {2} {@lambda x: .x::isgreater {20}}
    | Name | Age |
    | Anne | 24  |
```

You can omit `@lambda` only if the lambda body has no parameter references.

## Destructuring

When a lambda receives an iterable or pair, you can declare multiple parameters and they get unpacked component-by-component:

```markdown
.foreach {.mydictionary}
    key value:
    **.key**: .value
```

Conditions: the type must support destructuring (iterables, pairs, dictionaries-as-pairs), the lambda must receive a single argument, and you must declare more than one parameter.

## Variables

```markdown
.var {name} {Quarkdown}
Hello, .name!
```

Reassigning is just calling the variable as a function with a new value:

```markdown
.name {Markdown}
```

Or equivalently:

```markdown
.var {name} {Markdown}
```

Variables can hold block content too:

```markdown
.var {hero}
    .center
        ##! Welcome

        .loremipsum
```

For temporary scoped variables, use `.let`:

```markdown
.let {.multiply {4} {2}}
    area:
    The area is .area.
```

## Custom functions

```markdown
.function {greet}
    to from?:
    Hello, .to from .from::otherwise {unnamed}!

.greet {world}
.greet {world} {John}
```

- Trailing `?` on a parameter makes it optional. Missing args become `None`.
- The body parameter (last one) accepts block content via the body argument.
- Functions can return any value type — Markdown content, numbers, booleans, layouts.

## Conditionals

```markdown
.if {.iseven {3}}
    Three is even

.ifnot {.iseven {3}}
    Three is odd
```

There is no `else` keyword; emulate one with `.let`:

```markdown
.let {.iseven {3}}
    cond:
    .if {.cond}
        Even
    .ifnot {.cond}
        Odd
```

## Escaping

To write a literal `.` at the start of a line that would otherwise be parsed as a function, escape with `\`:

```markdown
\.this is not a function call.
```

Same for the auto-symbol replacements (see cheatsheet): `\->` keeps it as `->`.

## Including other files

```markdown
.include {chapter1.qd}                  # share context (default)
.include {setup.qd} sandbox:{share}     # explicit
.include {file.qd} sandbox:{scope}      # included file sees parent, but its declarations stay local
.include {file.qd} sandbox:{subdocument}  # full isolation, like a subdocument

.includeall                             # bulk
    - chapters/intro.qd
    - chapters/main.qd

.includeall {.listfiles {chapters} sortby:{name}}
```

## Subdocuments vs includes

- **`.include`**: pastes content into the current document, possibly sharing context. One output file.
- **Subdocuments**: separate files linked by Markdown links (`[Chapter 1](chapter1.qd)`). Each becomes its own output page. The `docs` doctype is built around this.

Both inherit setup from the main file, but subdocuments are fully isolated regarding declarations. Use `.subdocumentgraph` to render the navigation graph.
