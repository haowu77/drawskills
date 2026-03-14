# DrawSkills

> Professional diagram drawing skill for AI coding assistants via [drawio MCP](https://github.com/agentic-labs/drawio-mcp).

让 AI 编程助手通过 drawio MCP 绘制**专业级别**的技术图表。不只是简单的工具调用通道，更提供完整的布局算法、样式规范和图表类型专业知识。

## Features

- **6 种图表类型** — 流程图、架构图、时序图、ER 图、思维导图、用例图
- **智能布局引擎** — 根据节点数量自动计算坐标和间距，告别手动算 x/y
- **专业配色方案** — 5 套预定义配色 (Material Blue / Purple Dream / Forest Green / Warm Sunset / Monochrome)
- **自动连线规则** — 根据图表类型选择最佳连线方式和出入点
- **中文优化** — 字体大小、节点宽度、间距针对中文内容专门调优

## Quick Start

### 1. Prerequisites

- [drawio MCP server](https://github.com/agentic-labs/drawio-mcp) connected to your AI assistant
- An AI coding assistant that supports [Agent Skills](https://agentskills.io) (e.g., Claude Code)

### 2. Install

Clone this repo and add it as a skill directory:

```bash
git clone https://github.com/haowu77/drawskills.git
```

### 3. Use

Just tell your AI assistant:

```
画一个用户登录的流程图
```

```
Draw an architecture diagram for my project
```

```
帮我画个数据库 ER 图
```

The skill will automatically:
1. Identify the diagram type
2. Load the corresponding layout rules and style templates
3. Choose an appropriate color palette
4. Generate the drawio XML with precise coordinates
5. Export as SVG (recommended) or PNG

## Project Structure

```
drawskills/
├── CLAUDE.md              # Project instructions for AI assistants
├── SKILL.md               # AgentSkills.io skill definition
├── prompts/
│   ├── _base.md           # Shared base rules (XML structure, layout algorithm, styles)
│   ├── flowchart.md       # Flowchart-specific rules
│   ├── architecture.md    # Architecture diagram rules
│   ├── sequence.md        # Sequence diagram rules
│   ├── er-diagram.md      # ER diagram rules
│   ├── mindmap.md         # Mind map rules
│   └── usecase.md         # Use case diagram rules
├── palettes/
│   └── colors.md          # 5 color palette definitions
└── examples/
    └── *.svg              # Example outputs
```

## Diagram Types

| Type | Trigger Keywords | Layout |
|------|-----------------|--------|
| Flowchart | 流程图, flowchart | Top-down with decision branches |
| Architecture | 架构图, architecture | Layered with swimlanes |
| Sequence | 时序图, sequence | Lifelines with message arrows |
| ER Diagram | ER图, entity relationship | Grid with relationship lines |
| Mind Map | 思维导图, mindmap | Radial tree from center |
| Use Case | 用例图, use case | Actors + system boundary |

## How It Works

Each diagram type has a dedicated prompt file in `prompts/` that defines:

- **Layout algorithm** — coordinate calculation formulas, spacing rules, alignment
- **Shape mapping** — which drawio shapes to use for different semantic elements
- **Connection rules** — edge styles, arrow types, routing preferences
- **Size calculation** — node dimensions based on text content (with CJK width correction)

The base rules in `_base.md` provide shared foundations:
- drawio XML skeleton structure
- Adaptive layout parameters (loose/standard/compact based on node count)
- Universal style defaults (rounded corners, fonts, stroke width)
- Title and zone/swimlane conventions
- Quality checklist

## Example Output

Architecture diagram generated for [privacy-mask](https://github.com/fullstackcrew-alpha/privacy-mask):

![Privacy Mask Architecture](examples/privacy-mask-architecture.svg)

## Color Palettes

| Palette | Best For |
|---------|----------|
| **Material Blue** (default) | Architecture diagrams, general purpose |
| **Purple Dream** | Data pipelines, tech flows |
| **Forest Green** | DevOps, infrastructure |
| **Warm Sunset** | Product flows, user journeys |
| **Monochrome** | Print, formal documentation |

## License

[MIT](LICENSE)
