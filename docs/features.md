# Features & Comparison

[← Back to README](../README.md)

## Two Skills in This Repo

| | [`mermaid-skill`](../skills/mermaid-skill/) | [`mermaid-md`](../skills/mermaid-md/) |
| --- | --- | --- |
| **Input** | a natural-language request | a `.md` file that already contains ` ```mermaid ` blocks |
| **Output** | `.mmd` source + PNG / SVG / PDF | one image per block (+ optional Markdown rewrite) |
| **Source of truth** | the `.mmd` file | the Markdown file — no `.mmd` side files |
| **Backends** | `mmdc` local, Kroki API fallback | `mmdc` only (nothing leaves your machine) |
| **Use when** | you want to *author* a new diagram | the diagrams already live in your docs and you need pictures |

Details for the second one in [mermaid-md](#mermaid-md--markdown--images) below.

## Why This Skill?

| Feature | This Skill | Native Claude Code | Other Skills | MCP Server |
| --------- | ----------- | ------------------- | -------------- | ------------ |
| **Write Mermaid syntax** | Guided by examples | Built-in capability | Varies | Varies |
| **Validation before export** | Required step | No validation loop | Often skipped | Varies |
| **Export to PNG/SVG/PDF** | Automatic | Manual — user must ask | Usually one method | Web only |
| **Zero-install fallback** | Kroki needs only curl | No fallback | Requires install | Requires setup |
| **Proactive triggering** | Auto-triggers for 3+ components | Only when explicitly asked | Manual only | Manual |
| **Chinese language support** | Chinese keyword triggers | No keyword triggers | English only | English only |
| **End-to-end workflow** | Generate → Validate → Export → Report | Generate only | Partial | Partial |
| **Progressive disclosure** | Syntax in separate files | N/A | All inline | N/A |

**Key advantages over native Claude Code:**

- **Complete pipeline** — Claude Code can write Mermaid, but stops at `.mmd`. This skill adds validation, export, and error recovery automatically
- **Catches errors early** — validation loop prevents broken diagrams from being exported
- **Flexible export** — local mmdc or Kroki API fallback (no install needed)
- **Proactive diagramming** — auto-triggers when discussing architecture, not just when you ask for a diagram

## What This Skill Can Do

### Diagram Types (17+)

| Type | Use for | Example |
| ------ | --------- | --------- |
| **Flowchart** | Processes, pipelines, decision trees | CI/CD pipeline, user registration flow |
| **Sequence** | API calls, authentication flows | JWT auth, microservice communication |
| **Class** | OOP models, data structures | Domain models, inheritance hierarchies |
| **ER** | Database schemas | User-Order-Product relationships |
| **State** | State machines, lifecycles | Order status, connection states |
| **Gantt** | Project timelines | Sprint planning, release schedules |
| **Pie** | Proportions, distributions | Market share, resource allocation |
| **Git Graph** | Branch strategies | GitFlow, trunk-based development |
| **C4 Context** | High-level architecture | System context, container diagrams |
| **Mind Map** | Topic breakdowns | Feature planning, brainstorming |
| **Use Case** | Actor–system interactions (UML) | Login flow, role permissions |
| **Cynefin** | Sense-making / complexity domains | Incident response, strategy |
| **Event Modeling** | Event-driven system timelines | Cart add-to-order flow |
| **Tree View** | File / directory hierarchies | Project structure docs |
| **Wardley Maps** | Business strategy / value chains | Build vs. buy analysis |

### Output Formats

- **PNG** — High resolution (2048px), white background, multiple themes
- **SVG** — Scalable vector, perfect for docs
- **PDF** — Print-ready documents

### Automatic Triggering

The skill activates when you:

- Ask for diagrams explicitly: *"create a flowchart"*, *"draw architecture"*
- Explain complex systems: *"how does authentication work"* (3+ components)
- Use Chinese: *"画一个时序图"*, *"架构图"*

## mermaid-md — Markdown → Images

Design docs, RFCs and READMEs accumulate ` ```mermaid ` blocks. GitHub, GitLab and Obsidian
render them; Word, Confluence, PDF exports and slide decks do not. `mermaid-md` reads the
Markdown as-is and renders every block:

```bash
SKILL=skills/mermaid-md

python3 $SKILL/scripts/mermaid_md.py docs/design.md --list          # inventory
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/ # render all
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/ --in-place
```

### Capabilities

| Capability | Detail |
| --- | --- |
| **Block detection** | ` ``` ` and `~~~` fences, blocks indented inside list items; skips mermaid examples nested in a longer outer fence, other languages, and YAML front matter |
| **Source of truth** | the `.md` — errors report the block's line range (`design.md:633-654`) so you fix the diagram where it lives |
| **Selective render** | `--only 2,5-7` re-renders just the blocks you edited |
| **Validation** | `--check` renders every block into a temp dir and exits non-zero if any fails — drops straight into CI |
| **Embedding** | `--rewrite` / `--in-place` add the image **under** the block (`--rewrite-mode replace` swaps it out instead); re-running refreshes the image line instead of duplicating it |
| **Filenames** | `design-03-auth-flow.png`, from a `%% title:` comment, front-matter `title:`, or the nearest heading; accents folded to ASCII |
| **Toolchain recovery** | finds a usable Node >= 18 (an old conda/nvm Node is the usual cause of `SyntaxError: Unexpected token import`) and a Chrome/Chromium (puppeteer cache *or* system browser), retrying with `--no-sandbox` in containers |
| **Formats** | PNG (default, `-s 2` for crisp output), SVG, PDF |

### Key flags

| Flag | Meaning |
| --- | --- |
| `--list` | List blocks (index, line range, type, title) — renders nothing |
| `--check` | Validate only; non-zero exit on failure |
| `-o, --outdir` | Image output directory |
| `-f, --format` | `png` / `svg` / `pdf` |
| `--only` | Subset by index: `3`, `2,5`, `2-4,7` |
| `-s, --scale` | Pixel scale (default 2 for PNG) — the sharpness knob |
| `-t, --theme` | `default` / `dark` / `neutral` / `forest` |
| `--rewrite [OUT.md]` | Markdown copy with image links (default `<stem>.rendered.md`) |
| `--in-place` | Rewrite the input file itself |
| `--rewrite-mode` | `append` (default) / `replace` |
| `--chrome`, `--node` | Pin the browser / Node binary |

Full reference: [`skills/mermaid-md/SKILL.md`](../skills/mermaid-md/SKILL.md) ·
embedding guidance: [`skills/mermaid-md/reference/EMBEDDING.md`](../skills/mermaid-md/reference/EMBEDDING.md)
