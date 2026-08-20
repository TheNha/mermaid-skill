---
name: mermaid-md
description: Extract every ```mermaid code block from a Markdown file (README, docs, design notes) and render each one to PNG/SVG/PDF with the mmdc CLI, optionally rewriting the Markdown to reference the generated images. USE THIS SKILL when the input is a .md/.markdown file that already contains mermaid blocks and the user wants images, export, "render the diagrams in this doc", "xuất ảnh từ file markdown", "markdown 里的 mermaid 导出图片". Do NOT use it to author new diagrams from scratch — that is mermaid-skill.
homepage: https://github.com/Agents365-ai/creating-mermaid-diagrams
version: 1.0.0
metadata: {"openclaw":{"requires":{"bins":["python3","mmdc"]},"emoji":"🖼️"}}
---

# Mermaid in Markdown → Images

Input is a **Markdown file containing one or more ` ```mermaid ` blocks**. Output is one image
per block, plus (optionally) a rewritten Markdown that points at those images.

**The Markdown file is the single source of truth.** Never split diagrams out into `.mmd`
files — fix syntax errors in the `.md` block itself and re-render.

**Rendering is always local `mmdc`.** No API calls, nothing leaves the machine.

## When to use / when NOT to use

**Use this skill when:** a `.md` already holds the diagrams and you need image files — for
Word/PDF/Confluence/slide export, for renderers that don't support mermaid natively, or to
attach a picture to a PR or chat.

**Route elsewhere:**

- Authoring a *new* diagram, or wanting a standalone `.mmd` source → **mermaid-skill**.
- The target renders mermaid natively (GitHub, GitLab, Obsidian, Docusaurus) → no images
  needed; leave the block as text. See [reference/EMBEDDING.md](reference/EMBEDDING.md).

## Prerequisites

`python3`, the `mmdc` CLI, **Node >= 18**, and a Chrome/Chromium binary (mmdc renders
through Puppeteer):

```bash
npm install -g @mermaid-js/mermaid-cli
node -v      # must be >= 18 — mermaid-cli 11 is ESM-only
mmdc --version
```

**About Node — the environment trap.** `mmdc` is a `#!/usr/bin/env node` script, so it runs
under whatever `node` is first on `PATH`. An active conda env, an old nvm default, or a
project-local toolchain can shadow the system Node and produce:

```
SyntaxError: Unexpected token import
```

That is an old Node, not a broken diagram or a missing browser. The script checks
`node -v` up front and, if it is too old, hunts for a newer one (`/usr/bin/node`,
`/usr/local/bin`, `/opt/homebrew/bin`, nvm, volta) and invokes mmdc's entry point through
it — so it works even inside an activated conda env. Override with `--node /path/to/node`.
It prints the Node and Chrome it settled on:

```
renderer: mmdc (node v20 /usr/bin/node, chrome: /bin/google-chrome)
```

**About Chrome — read this before "fixing" a diagram.** `mmdc --version` succeeds even with
no browser at all; the failure only appears at render time as `Could not find Chrome`. That
is a *setup* error, never a syntax error.

The script resolves a browser itself, in this order, and prints the one it picked:

1. `--chrome /path/to/chrome`, or `PUPPETEER_EXECUTABLE_PATH`
2. Puppeteer's own cached browser (`npx puppeteer browsers install chrome-headless-shell`)
3. A system browser on `PATH` — `google-chrome`, `google-chrome-stable`, `chromium`,
   `chromium-browser`, `microsoft-edge`, or the macOS `.app` bundles

It also retries with `--no-sandbox` automatically when the sandbox is unavailable (root, or
a container). So an existing system Chrome is enough — no puppeteer download required. If
none of the three work, it exits with every attempt and its error, plus the fix.

## Workflow

1. **Inventory** — `--list` the blocks first. Cheap, and it tells you how many diagrams,
   what types, and which lines they live on.
2. **Render** — run the script over the whole file (or `--only` a subset).
3. **Fix failures at the source** — every failure prints `file.md:<start>-<end>`, the block's
   line range in the Markdown. Edit that block in the `.md`, then re-run with `--only N`.
   (Mermaid's own "Parse error on line N" counts tokens, not source lines — trust the range.)
4. **Self-check (vision)** — read the produced PNGs and fix readability defects (clipped
   labels, cramped layout, wrong orientation). Max 2 rounds. See table below.
5. **Embed (optional)** — `--rewrite` to emit a Markdown copy referencing the images, or
   `--in-place` to update the original. The mermaid block **stays** and the image is added
   below it (`--rewrite-mode replace` drops the block instead). Re-running is idempotent:
   an image already sitting under a block is refreshed, not duplicated. Only do this when
   the user asked for it.
6. **Report** — list the image paths and any block that still fails.

## The script

```bash
SKILL=skills/mermaid-md   # path to this skill

# 1. What's in the file?
python3 $SKILL/scripts/mermaid_md.py docs/design.md --list

# 2. Render every block to PNG in assets/
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o assets/

# 3. Validate only (renders to a temp dir, keeps nothing)
python3 $SKILL/scripts/mermaid_md.py docs/design.md --check

# 4. Re-render just blocks 2 and 5 after an edit
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o assets/ --only 2,5

# 5. Render + produce docs/design.rendered.md: mermaid block kept, image added under it
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o assets/ --rewrite

# 6. Same, updating the doc itself (idempotent — safe to re-run after editing a diagram)
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o assets/ --in-place
```

| Flag | Meaning |
| --- | --- |
| `--list` | List blocks (index, line range, type, title) — renders nothing |
| `--check` | Render to a temp dir to validate syntax; exit 1 if any block fails |
| `-o, --outdir` | Image output directory (default `.`) |
| `-f, --format` | `png` (default) / `svg` / `pdf` |
| `--only` | Subset by index: `3`, `2,5`, `2-4,7` |
| `--prefix` | Image name prefix (default: the Markdown file's stem) |
| `-s, --scale` | Pixel scale, **default 2** for PNG — the real sharpness knob; raise to 3–4 for print |
| `-w, --width` | Render viewport width, default `2048` (affects wrapping, not resolution) |
| `-t, --theme` | `default` / `dark` / `neutral` / `forest` |
| `-b, --background` | Background color, default `white` (use `transparent` for slides) |
| `-c, --config` | Mermaid config JSON (fonts, `themeVariables`, `flowchart` options) |
| `-p, --puppeteer-config` | Puppeteer config JSON, if you need custom browser flags |
| `--chrome` | Explicit Chrome/Chromium binary |
| `--node` | Explicit `node` binary to run mmdc with (>= v18) |
| `--rewrite [OUT.md]` | Write a Markdown copy with image links (default `<stem>.rendered.md`) |
| `--in-place` | Rewrite the input file itself (destructive — confirm first) |
| `--rewrite-mode` | `append` (default) keeps the mermaid block and puts the image under it; `replace` swaps the block out for the image |

Exit code is non-zero when any selected block fails, so it drops straight into CI.

### Image naming

`<stem>-<NN>-<slug>.png`, e.g. `design-03-auth-flow.png`. The slug comes from, in order:

1. `%% title: Auth Flow` on the first lines of the block,
2. a `title:` in the block's YAML front matter (`--- title: … ---`),
3. the nearest preceding Markdown heading,
4. the diagram type.

Accents are folded to ASCII, so a Vietnamese heading yields
`doc-01-quy-trinh-thu-thap-tung-log.png` rather than mangled dashes.

Add a `%% title:` comment to a block when you want a stable, meaningful filename — it is a
mermaid comment, so it stays invisible in native renderers.

### What counts as a block

Fenced with ` ``` ` or `~~~`, info string starting with `mermaid`. Correctly **skipped**:
mermaid fences nested inside a longer outer fence (docs showing mermaid examples), fences
in other languages, and YAML front matter. Indented blocks (inside list items) are picked
up and de-indented.

## Self-Check (vision)

A block that renders is not necessarily a block that *reads*. After rendering, look at the
PNGs and fix the source block:

| Check | Look for | Fix in the `.md` block |
| --- | --- | --- |
| Label truncation | Node/edge text clipped | Shorten the label or wrap with `<br/>` |
| Cramped density | Nodes crammed, tangled edges | Flip `TD`↔`LR`, add `subgraph`s, cut nodes |
| Wrong aspect | Far too wide or too tall | Change direction |
| Edge spaghetti | Many crossings | Reorder declarations so linked nodes are adjacent |
| Low contrast | Text blends into fill | Adjust `classDef` / pick another `-t` theme |
| Blurry image | Text soft at 100% zoom | Raise `-s` (3 or 4), not `-w` |

Max **2 rounds**; re-render with `--only N` after each fix. Skip if vision is unavailable.

## Alternative: mmdc's native Markdown mode

`mmdc` can consume Markdown directly:

```bash
mmdc -i docs/design.md -o docs/design-out.md   # writes design-out-1.png, -2.png, …
```

Use the script instead when you need: `--list`/`--check`, selective re-render, meaningful
filenames, automatic Chrome discovery, per-block error locations, or a rewrite that keeps
the original code block.

## Diagram syntax

Block syntax is plain Mermaid — see the sibling **mermaid-skill** `reference/` files
(FLOWCHART, SEQUENCE, CLASS-ER, ARCHITECTURE, USECASE, OTHER-TYPES) or paste the block into
[mermaid.live](https://mermaid.live) to debug interactively.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Splitting blocks into `.mmd` files to render them | Don't — render from the `.md`; the script handles extraction |
| `SyntaxError: Unexpected token import` | Old Node from an active env (conda/nvm). `conda deactivate`, or pass `--node /usr/bin/node` — the script auto-recovers in most cases |
| Treating `Could not find Chrome` as a diagram bug | It's setup. Don't rewrite valid mermaid — point `--chrome` at a browser or install `chrome-headless-shell` |
| `mmdc --version` passed, so "mmdc works" | It proves nothing about rendering; only a real render does. Use `--check` |
| Raising `-w` to fix a blurry PNG | `-w` is viewport width; sharpness comes from `-s` |
| Absolute image paths in the rewritten `.md` | The script writes paths relative to the output `.md` — keep images inside the doc tree |
| Re-rendering everything after one edit | Use `--only N` |
| Overwriting the user's doc unasked | `--in-place` is destructive; default to `--rewrite` and confirm |
| Nested example block rendered by accident | Wrap doc examples in a 4-backtick outer fence — the script then skips them |
