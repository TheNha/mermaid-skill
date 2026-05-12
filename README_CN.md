# Mermaid 图表技能 —— 从代码到图片,一步到位

[English](README.md) | [在线文档](https://agents365-ai.github.io/mermaid-skill/zh.html)

一个生成、校验并自动导出 Mermaid 图表为 PNG / SVG / PDF 的技能 —— 内置导出前的语法校验环节,本地 `mmdc` 不可用时自动回退到 Kroki API。

支持 Claude Code、Cursor、Copilot、OpenClaw、Codex 等任何兼容 [Agent Skills](https://agentskills.io) 规范的 agent。

## 文档导航

| 文档 | 内容 |
|---|---|
| [docs/features_CN.md](docs/features_CN.md) | 为什么选这个技能、能力对比、图表类型、输出格式、自动触发 |
| [docs/workflow_CN.md](docs/workflow_CN.md) | 验证优先的工作流图与文件结构 |
| [skills/mermaid-skill/SKILL.md](skills/mermaid-skill/SKILL.md) | agent 加载的工作流指南 |

## 快速开始

安装技能:

```bash
# 任意 agent(Claude Code、Cursor、Copilot 等)
npx skills add Agents365-ai/365-skills -g

# 仅 Claude Code
> /plugin marketplace add Agents365-ai/365-skills
> /plugin install mermaid
```

手动安装 —— 克隆到你的 agent skills 目录:

```bash
git clone https://github.com/Agents365-ai/mermaid-skill.git ~/.claude/skills/mermaid-skill
```

常用路径:`~/.claude/skills/`(Claude Code)、`~/.config/opencode/skills/`(Opencode)、`~/.openclaw/skills/`(OpenClaw)、`~/.agents/skills/`(Codex)。同时已索引于 [SkillsMP](https://skillsmp.com)。

安装依赖,两选一:

```bash
# 方式 A —— 本地导出(最佳质量)
npm install -g @mermaid-js/mermaid-cli && mmdc --version

# 方式 B —— Kroki API(无需安装,只要 curl)
curl --version
```

## 使用方式

在 Claude Code 中描述你想要的:

```
> 创建一个用户认证的时序图,使用 JWT

> 画一个电商微服务架构图
```

技能会生成 `.mmd` 源码、**自动校验语法**、导出为 PNG/SVG/PDF,并报告输出路径。

## 示例输出

**提示词:** *创建一个微服务电商架构,包含 API 网关、各种服务和数据库*

![微服务架构](assets/example.png)

## 支持作者

如果这个技能对你有帮助,欢迎支持作者:

<table>
  <tr>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/wechat-pay.png" width="180" alt="微信支付">
      <br>
      <b>微信支付</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/alipay.png" width="180" alt="支付宝">
      <br>
      <b>支付宝</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/buymeacoffee.png" width="180" alt="Buy Me a Coffee">
      <br>
      <b>Buy Me a Coffee</b>
    </td>
  </tr>
</table>

## 作者

**Agents365-ai**

- GitHub: https://github.com/Agents365-ai
- Bilibili: https://space.bilibili.com/441831884

## License

MIT
