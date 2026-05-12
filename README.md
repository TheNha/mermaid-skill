# Mermaid Diagrams — From Code to Image, Automatically

[中文文档](README_CN.md)

A skill that generates, validates, and exports Mermaid diagrams to PNG / SVG / PDF — with a validation loop that catches syntax errors before export, and a Kroki API fallback when local `mmdc` isn't available.

Works with Claude Code, Cursor, Copilot, OpenClaw, Codex, and any agent that supports the [Agent Skills](https://agentskills.io) format.

## Documentation

| Doc | What's inside |
|---|---|
| [docs/features.md](docs/features.md) | Why this skill, capability matrix, diagram types, output formats, auto-triggering |
| [docs/workflow.md](docs/workflow.md) | Validation-first workflow diagram and file structure |
| [skills/mermaid-skill/SKILL.md](skills/mermaid-skill/SKILL.md) | Workflow guide loaded by the agent |

## Quick Start

Install the skill:

```bash
# Any agent (Claude Code, Cursor, Copilot, etc.)
npx skills add Agents365-ai/365-skills -g

# Claude Code only
> /plugin marketplace add Agents365-ai/365-skills
> /plugin install mermaid
```

Manual install — clone into your agent's skills directory:

```bash
git clone https://github.com/Agents365-ai/mermaid-skill.git ~/.claude/skills/mermaid-skill
```

Common paths: `~/.claude/skills/` (Claude Code), `~/.config/opencode/skills/` (Opencode), `~/.openclaw/skills/` (OpenClaw), `~/.agents/skills/` (Codex). Also indexed on [SkillsMP](https://skillsmp.com).

Install dependencies — pick one:

```bash
# Option A — local export (best quality)
npm install -g @mermaid-js/mermaid-cli && mmdc --version

# Option B — Kroki API (no install, needs only curl)
curl --version
```

## Usage

Just describe what you want:

```
> Create a sequence diagram for user authentication with JWT

> Draw an e-commerce microservices architecture
```

The skill generates the `.mmd` source, **validates syntax**, exports to PNG/SVG/PDF, and reports the output paths.

## Example Output

**Prompt:** *Create a microservices e-commerce architecture with API Gateway, services, and databases*

![Microservices Architecture](assets/example.png)

## Support

If this skill helps you, consider supporting the author:

<table>
  <tr>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/wechat-pay.png" width="180" alt="WeChat Pay">
      <br>
      <b>WeChat Pay</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/alipay.png" width="180" alt="Alipay">
      <br>
      <b>Alipay</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/buymeacoffee.png" width="180" alt="Buy Me a Coffee">
      <br>
      <b>Buy Me a Coffee</b>
    </td>
  </tr>
</table>

## Author

**Agents365-ai**

- GitHub: https://github.com/Agents365-ai
- Bilibili: https://space.bilibili.com/441831884

## License

MIT
