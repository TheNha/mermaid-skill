# 功能特性与对比

[← 返回 README](../README_CN.md)

## 本仓库的两个技能

| | [`mermaid-skill`](../skills/mermaid-skill/) | [`mermaid-md`](../skills/mermaid-md/) |
| --- | --- | --- |
| **输入** | 一句自然语言需求 | 已含 ` ```mermaid ` 块的 `.md` 文件 |
| **输出** | `.mmd` 源码 + PNG / SVG / PDF | 每块一张图(可选改写 Markdown) |
| **唯一真相** | `.mmd` 文件 | Markdown 文件 —— 不产生 `.mmd` 副本 |
| **后端** | 本地 `mmdc`,可回退 Kroki | 仅 `mmdc`(数据不出本机) |
| **何时用** | 要**新画**一张图 | 图已写在文档里,只是需要图片 |

第二个技能的细节见下方 [mermaid-md](#mermaid-md--markdown--图片)。

## 为什么选择这个技能?

| 特性 | 本技能 | 原生 Claude Code | 其他技能 | MCP 服务器 |
| ------ | -------- | ----------------- | ---------- | ------------ |
| **编写 Mermaid 语法** | 有示例引导 | 内置能力 | 不一定 | 不一定 |
| **导出前验证** | 必须步骤 | 无验证环节 | 经常跳过 | 不一定 |
| **导出 PNG/SVG/PDF** | 自动完成 | 需手动要求 | 通常只有一种 | 仅网页 |
| **零安装备选** | Kroki 只需 curl | 无备选方案 | 需要安装 | 需要配置 |
| **主动触发** | 3+ 组件自动触发 | 仅在明确要求时 | 仅手动 | 仅手动 |
| **中文支持** | 画图、架构图、流程图、时序图 | 无关键词触发 | 仅英文 | 仅英文 |
| **端到端工作流** | 生成→验证→导出→报告 | 仅生成代码 | 部分支持 | 部分支持 |
| **渐进式加载** | 语法分离到独立文件 | 不适用 | 全部内联 | 不适用 |

**相比原生 Claude Code 的核心优势:**

- **完整管道** — Claude Code 能写 Mermaid 代码,但止步于 `.mmd` 文件。本技能自动完成验证、导出和错误恢复
- **提前捕获错误** — 验证循环防止导出损坏的图表
- **灵活导出** — 本地 mmdc 或 Kroki API 备选(无需安装)
- **主动画图** — 讨论架构时自动触发,不需要你专门要求"画个图"

## 这个技能能做什么

### 图表类型(17+ 种)

| 类型 | 用途 | 示例 |
| ------ | ------ | ------ |
| **流程图** | 流程、管道、决策树 | CI/CD 管道、用户注册流程 |
| **时序图** | API 调用、认证流程 | JWT 认证、微服务通信 |
| **类图** | OOP 模型、数据结构 | 领域模型、继承层次 |
| **ER 图** | 数据库模式 | 用户-订单-商品关系 |
| **状态图** | 状态机、生命周期 | 订单状态、连接状态 |
| **甘特图** | 项目时间线 | 迭代规划、发布计划 |
| **饼图** | 比例、分布 | 市场份额、资源分配 |
| **Git 图** | 分支策略 | 分支模型、主干开发 |
| **C4 上下文图** | 高级架构 | 系统上下文、容器图 |
| **思维导图** | 主题分解 | 功能规划、头脑风暴 |
| **用例图** | 参与者与系统交互 (UML) | 登录流程、角色权限 |
| **Cynefin** | 复杂度域 / 决策框架 | 事件响应、战略分析 |
| **事件建模** | 事件驱动系统时间线 | 购物车下单流程 |
| **树视图** | 文件 / 目录层级 | 项目结构文档 |
| **Wardley 地图** | 商业战略 / 价值链 | 自建 vs 购买分析 |

### 输出格式

- **PNG** — 高分辨率(2048px),白色背景,多种主题
- **SVG** — 可缩放矢量图,适合文档
- **PDF** — 可打印文档

### 自动触发

技能在以下情况自动激活:

- 明确请求图表:*"创建流程图"*、*"画架构图"*
- 解释复杂系统:*"认证是怎么工作的"*(3+ 组件)
- 使用中文:*"画一个时序图"*、*"架构图"*

## mermaid-md —— Markdown → 图片

设计文档、RFC、README 里往往攒了一堆 ` ```mermaid ` 代码块。GitHub、GitLab、Obsidian 能直接渲染,
但 Word、Confluence、PDF 导出和幻灯片不能。`mermaid-md` 直接读这份 Markdown,把每个块渲染出来:

```bash
SKILL=skills/mermaid-md

python3 $SKILL/scripts/mermaid_md.py docs/design.md --list          # 清点
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/ # 全部渲染
python3 $SKILL/scripts/mermaid_md.py docs/design.md -o docs/assets/ --in-place
```

### 能力

| 能力 | 说明 |
| --- | --- |
| **块识别** | 支持 ` ``` ` 与 `~~~` 围栏、列表内缩进的块;跳过嵌套在更长围栏里的 mermaid 示例、其它语言代码块与 YAML front matter |
| **唯一真相** | `.md` 本身 —— 报错给出该块的行号区间(`design.md:633-654`),就地改图 |
| **按需重渲** | `--only 2,5-7` 只重渲改动过的块 |
| **校验** | `--check` 把每块渲染到临时目录,有失败即非零退出,可直接进 CI |
| **嵌入** | `--rewrite` / `--in-place` 把图片加在代码块**下方**(`--rewrite-mode replace` 才替换掉代码块);重复运行只刷新那行图片,不会重复堆叠 |
| **文件名** | `design-03-auth-flow.png`,取自 `%% title:` 注释、front matter `title:` 或最近的标题;非英文标题折叠成 ASCII |
| **工具链自恢复** | 自己找可用的 Node >= 18(旧 conda/nvm Node 是 `SyntaxError: Unexpected token import` 的常见原因)和 Chrome/Chromium(puppeteer 缓存**或**系统浏览器),容器内自动用 `--no-sandbox` 重试 |
| **格式** | PNG(默认,`-s 2` 更清晰)、SVG、PDF |

### 常用参数

| 参数 | 含义 |
| --- | --- |
| `--list` | 列出所有块(序号、行号区间、类型、标题),不渲染 |
| `--check` | 仅校验,失败则非零退出 |
| `-o, --outdir` | 图片输出目录 |
| `-f, --format` | `png` / `svg` / `pdf` |
| `--only` | 按序号取子集:`3`、`2,5`、`2-4,7` |
| `-s, --scale` | 像素倍率(PNG 默认 2)—— 控制清晰度的关键参数 |
| `-t, --theme` | `default` / `dark` / `neutral` / `forest` |
| `--rewrite [OUT.md]` | 生成带图片链接的 Markdown 副本(默认 `<stem>.rendered.md`) |
| `--in-place` | 直接改写输入文件 |
| `--rewrite-mode` | `append`(默认)/ `replace` |
| `--chrome`、`--node` | 指定浏览器 / Node 可执行文件 |

完整参数见 [`skills/mermaid-md/SKILL.md`](../skills/mermaid-md/SKILL.md) ·
嵌入建议见 [`skills/mermaid-md/reference/EMBEDDING.md`](../skills/mermaid-md/reference/EMBEDDING.md)
