# readable-coding

English | [简体中文](./README.zh.md)

A skill for AI coding assistants. It makes the assistant write **R, Python and Linux command-line code that people can read and run themselves**, instead of long, over-engineered code.

## Why

Code written by AI assistants is often correct but hard to read. Common problems:
- many steps packed into one code block;
- re-implementing functions that already exist in packages;
- adding checks and options nobody asked for;
- spending a lot of code on styling plots.

This skill gives the assistant a set of rules so that the delivered code looks like well-written teaching code.

## What's inside

| Section | Content |
|---|---|
| Before coding | Ask when the request is ambiguous; state any parameters the assistant chose |
| General rules | Do only what was asked; one step per block; use existing tools; default output and default plots; a step / input / output comment at the top of every block; no over-abstraction; parameters at the top; plain names; surgical edits; simplicity test |
| R | `package::function()`, R Markdown, `set.seed()`, base plots |
| Python | Ready-made pandas / scikit-learn functions, Jupyter cells, `random_state`; no unrequested `argparse` / `class` / `logging` / broad `try/except` |
| Linux command line | One command per line, short pipes, standard tools, variables defined at the top and quoted |
| Rendered reports (R Markdown / Quarto) | Knit to HTML with numbered sections, a floating table of contents and code folding (code shown by default); a short explanation before each chunk, results shown as tables, a sample-flow table, `sessionInfo()` at the end |
| Verification | The assistant checks its own results; the checking code is not included in the delivered code |
| Checklist and examples | A pre-delivery checklist, plus one "don't write this / write this" pair each for R, Python and bash |

The rules in `SKILL.md` are written in English.

## Usage

`SKILL.md` uses the standard skill format (`name` + `description` in the frontmatter), so the same file works in Claude and Codex. Clone this repository into the skills folder of the tool you use.

**Claude Code**

```bash
git clone https://github.com/alexpengyl1/readable-coding.git ~/.claude/skills/readable-coding
```

**Codex (OpenAI)**

```bash
git clone https://github.com/alexpengyl1/readable-coding.git ~/.agents/skills/readable-coding
```

To use it in one project only, clone it into `.claude/skills/readable-coding` (Claude Code) or `.agents/skills/readable-coding` (Codex) inside the project. Restart the tool if the skill does not show up. To update later: `git -C <skill folder> pull`.

**Claude app (claude.ai / desktop)**: download this repository as a ZIP and upload it under Settings → Capabilities → Skills.

- **Other assistants (Cursor, Copilot, etc.)**: paste the body of `SKILL.md` (everything after the block between the opening `---` lines) into the assistant's rules file or custom instructions.

## Acknowledgements

- The code style follows the teaching code of the ML4LS course (Payam Emami, payamemami.com).
- Some principles are adapted from [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) (`karpathy-guidelines`, MIT License), including Think Before Coding, Simplicity First, Surgical Changes and Goal-Driven Execution.
