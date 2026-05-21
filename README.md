# Quarkdown Skill

[![Install](https://skills.sh/b/uditya-kumar/quarkdown-skill)](https://skills.sh/s/uditya-kumar/quarkdown-skill)

A Claude [Skill](https://www.anthropic.com/news/claude-skills) for authoring [Quarkdown](https://github.com/iamgio/quarkdown) (`.qd`) documents: books, articles, reports, slides, wikis, PDFs.

Quarkdown is a Turing-complete Markdown flavor with functions, variables, loops, and conditionals — a programmable alternative to LaTeX.

## Install

```shell
npx skills add uditya-kumar/quarkdown-skill
```

## What this skill provides

| Capability | Description |
|---|---|
| Decision guidance | Selects `.doctype` (`plain`, `paged`, `slides`, `docs`) and theme. |
| Syntax discipline | Enforces `.fn {arg} named:{value}`, inline vs block-body calls, `::` chaining, `{...}` tight calls, `\` line continuation. |
| Cookbook | Maps needs (columns, callouts, figures, equations, fragments, footnotes, TOCs, covers) to the correct call. |
| Failure-mode awareness | Avoids double page breaks before `#`, lazy-line paragraph merging, inline/body confusion, missing figure captions, page counters in `plain` docs. |

## Repository layout

| Path | Role |
|---|---|
| [SKILL.md](SKILL.md) | Entry point — decisions and rules in working memory. |
| [references/syntax.md](references/syntax.md) | Argument forms, body args, chaining, lambdas, escaping. |
| [references/functions.md](references/functions.md) | Stdlib signatures: math, string, collection, layout, document, slides, html. |
| [references/cookbook.md](references/cookbook.md) | Recipes: layouts, slides, tables, charts, bibliographies, subdocs, themes. |
| [references/cheatsheet.md](references/cheatsheet.md) | One-pager: symbols, images, footnotes, quotes, gotchas. |
| [assets/templates/plain.qd](assets/templates/plain.qd) | Continuous-flow starter. |
| [assets/templates/paged.qd](assets/templates/paged.qd) | Book / report starter with cover + TOC. |
| [assets/templates/slides.qd](assets/templates/slides.qd) | Presentation starter. |
| [assets/templates/docs.qd](assets/templates/docs.qd) | Wiki / docs-site starter. |

References load on demand; `SKILL.md` stays lean.

## Installation

| Client | Location |
|---|---|
| Claude Code (user) | `~/.claude/skills/quarkdown/` |
| Claude Code (project) | `<project>/.claude/skills/quarkdown/` |
| Claude Desktop / API | See [Skills docs](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview). |

Auto-activates on `.qd` files, Quarkdown syntax questions, or requests for slides / paged PDFs / wikis from Markdown-like sources.

## License

Same terms as the surrounding project. Quarkdown: [iamgio/quarkdown](https://github.com/iamgio/quarkdown).
