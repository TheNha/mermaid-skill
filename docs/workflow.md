# Workflow & File Structure

[← Back to README](../README.md)

## How It Works

This skill follows a validation-first workflow:

![Skill Workflow](../assets/workflow.png)

<details>
<summary>View Mermaid source</summary>

```mermaid
flowchart TD
  Start([User requests diagram]):::input --> CheckDeps{Check deps}:::decision
  CheckDeps -->|mmdc available| UseMmdc[Use mmdc locally]:::process
  CheckDeps -->|mmdc unavailable| UseKroki[Use Kroki API]:::process
  UseMmdc --> PickType
  UseKroki --> PickType
  PickType[Pick diagram type]:::process --> Generate[Generate .mmd file]:::process
  Generate --> Validate{Validate syntax}:::decision
  Validate -->|Error| Fix[Fix .mmd file]:::warning
  Fix --> Validate
  Validate -->|Pass| Export[Export PNG/SVG/PDF]:::process
  Export --> Report([Report output paths]):::output

  classDef input fill:#d4edda,stroke:#28a745,color:#155724
  classDef process fill:#cce5ff,stroke:#007bff,color:#004085
  classDef decision fill:#fff3cd,stroke:#ffc107,color:#856404
  classDef warning fill:#f8d7da,stroke:#dc3545,color:#721c24
  classDef output fill:#e2d5f1,stroke:#6f42c1,color:#4a235a
```

**Color legend:** 🟢 Input | 🔵 Process | 🟡 Decision | 🔴 Warning | 🟣 Output

</details>

## mermaid-md — the Markdown-first workflow

`mermaid-md` starts from a Markdown file that already contains ` ```mermaid ` blocks. The
Markdown stays the source of truth: failures point back at the block's line range, you fix
it there, and re-render just that block.

![mermaid-md workflow](../assets/workflow-md.png)

<details>
<summary>View Mermaid source</summary>

```mermaid
flowchart TD
  Start([Markdown file with mermaid blocks]):::input --> List[List blocks: index, lines, type]:::process

  List --> Resolve{Resolve toolchain}:::decision
  Resolve -->|Node < 18 or no Chrome| Recover[Find newer Node / system Chrome]:::warning
  Recover --> Resolve
  Resolve -->|mmdc can render| Render[Render each block to PNG/SVG/PDF]:::process

  Render --> Ok{All blocks rendered?}:::decision
  Ok -->|Failure| FixBlock["Fix that block in the .md<br/>error names design.md:633-654"]:::warning
  FixBlock --> Only[Re-render with --only N]:::process
  Only --> Ok

  Ok -->|Yes| SelfCheck[Vision self-check the images]:::process
  SelfCheck --> Embed[Optional: add image under each block]:::process
  Embed --> Report([Report image paths]):::output

  classDef input fill:#d4edda,stroke:#28a745,color:#155724
  classDef process fill:#cce5ff,stroke:#007bff,color:#004085
  classDef decision fill:#fff3cd,stroke:#ffc107,color:#856404
  classDef warning fill:#f8d7da,stroke:#dc3545,color:#721c24
  classDef output fill:#e2d5f1,stroke:#6f42c1,color:#4a235a
```

**Color legend:** 🟢 Input | 🔵 Process | 🟡 Decision | 🔴 Warning | 🟣 Output

</details>

```bash
SKILL=skills/mermaid-md

python3 $SKILL/scripts/mermaid_md.py docs/design.md --list           # 1. inventory
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/  # 2. render
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/ --only 3   # 3. after a fix
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/ --in-place # 4. embed
```

Step 4 keeps the mermaid block and puts the image underneath it, and re-running refreshes
that image line instead of adding another — so it is safe in a pre-commit hook. Details in
[`skills/mermaid-md/SKILL.md`](../skills/mermaid-md/SKILL.md).

## File Structure

```
mermaid-skill/
├── skills/
│   ├── mermaid-skill/              # author diagrams from a prompt
│   │   ├── SKILL.md
│   │   └── reference/
│   │       ├── FLOWCHART.md        # flowchart syntax & examples
│   │       ├── SEQUENCE.md         # sequence diagram syntax
│   │       ├── CLASS-ER.md         # class & ER diagram syntax
│   │       ├── ARCHITECTURE.md     # architecture-beta syntax
│   │       ├── USECASE.md          # use case syntax
│   │       └── OTHER-TYPES.md      # state, Gantt, git, pie, mindmap, C4
│   ├── mermaid-md/                 # render mermaid blocks inside a .md
│   │   ├── SKILL.md
│   │   ├── scripts/
│   │   │   └── mermaid_md.py       # extract → render → optional rewrite
│   │   └── reference/
│   │       └── EMBEDDING.md        # which targets need images, paths, CI
│   └── md-to-docx/                 # Markdown → Word (.docx)
│       ├── SKILL.md                # vendored from github/awesome-copilot (MIT)
│       ├── NOTICE.md               # upstream commit + how to refresh it
│       ├── LICENSE
│       └── scripts/
│           ├── md-to-docx.mjs
│           └── package.json
├── assets/                         # example .mmd sources + rendered PNGs
├── docs/                           # this documentation site
├── README.md                       # English docs (default)
└── README_CN.md                    # Chinese docs
```
