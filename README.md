# DrawSkills

> Professional multi-engine diagram drawing skill for AI coding assistants. Supports [draw.io MCP](https://github.com/agentic-labs/drawio-mcp), Mermaid, and Excalidraw.

[中文文档](README.zh-CN.md)

Draw **professional-grade** technical diagrams through AI coding assistants with multiple rendering engines. Not just a tool-calling wrapper — provides complete layout algorithms, style specifications, and diagram-type expertise for each engine.

## Features

- **Multi-engine support** — draw.io (via MCP), Mermaid (.mmd), Excalidraw (.excalidraw JSON)
- **Extensible diagram types** — Built-in flowchart, architecture, sequence, ER, mind map, use case; easy to add your own
- **Smart layout engine** — Automatically calculates coordinates and spacing based on node count
- **Professional color palettes** — 5 presets per engine (Material Blue / Purple Dream / Forest Green / Warm Sunset / Monochrome)
- **Auto-routing rules** — Selects optimal connection styles and entry/exit points per diagram type
- **CJK optimized** — Font sizes, node widths, and spacing tuned for Chinese content

## Supported Engines

| Engine | Output Format | Dependency | Best For |
|--------|--------------|------------|----------|
| **draw.io** | drawio XML → SVG/PNG | drawio MCP server | Rich diagrams with precise layout control |
| **Mermaid** | `.mmd` text file | None (always available) | Quick diagrams, version-control friendly |
| **Excalidraw** | `.excalidraw` JSON | None (always available) | Hand-drawn style, whiteboard scenarios |

## Quick Start

### 1. Prerequisites

- An AI coding assistant that supports [Agent Skills](https://agentskills.io) (e.g., Claude Code)
- Optional: [drawio MCP server](https://github.com/agentic-labs/drawio-mcp) for draw.io engine

### 2. Install

Clone this repo and add it as a skill directory:

```bash
git clone https://github.com/haowu77/drawskills.git
```

### 3. Use

Just tell your AI assistant:

```
Draw a login flowchart
```

```
用 mermaid 画个时序图
```

```
Draw an architecture diagram with excalidraw
```

The skill will automatically:
1. Select the best engine (user preference > MCP availability > default)
2. Scan `prompts/{engine}/` to discover available diagram types
3. Match your intent to the best diagram type via trigger keywords
4. Load the corresponding layout rules and style templates
5. Choose an appropriate color palette
6. Generate output in the engine's native format

## Project Structure

```
drawskills/
├── CLAUDE.md                       # Project instructions for AI assistants
├── SKILL.md                        # AgentSkills.io skill definition
├── engines.md                      # Engine registry + selection rules
├── prompts/
│   ├── _shared.md                  # Engine-agnostic shared rules
│   ├── drawio/                     # draw.io engine prompts
│   │   ├── _base.md               # XML structure, layout, styles
│   │   └── *.md                   # Diagram type rules
│   ├── mermaid/                    # Mermaid engine prompts
│   │   ├── _base.md               # Mermaid syntax basics
│   │   └── *.md                   # Diagram type rules
│   └── excalidraw/                 # Excalidraw engine prompts
│       ├── _base.md               # JSON structure, elements
│       └── *.md                   # Diagram type rules
├── palettes/
│   ├── colors.md                  # draw.io color palettes
│   ├── mermaid-themes.md          # Mermaid theme configurations
│   └── excalidraw-colors.md       # Excalidraw color properties
└── examples/
    ├── drawio/                    # draw.io example outputs
    ├── mermaid/                   # Mermaid example outputs
    └── excalidraw/                # Excalidraw example outputs
```

## Built-in Diagram Types

| Type | draw.io | Mermaid | Excalidraw | Trigger Keywords |
|------|---------|---------|------------|-----------------|
| Flowchart | ✅ | ✅ | ✅ | flowchart, flow, process, 流程图 |
| Architecture | ✅ | ❌ | ✅ | architecture, arch, system design, 架构图 |
| Sequence | ✅ | ✅ | ❌ | sequence, interaction, 时序图 |
| ER Diagram | ✅ | ✅ | ❌ | ER diagram, data model, 实体关系 |
| Mind Map | ✅ | ✅ | ❌ | mindmap, brainstorm, 思维导图 |
| Use Case | ✅ | ❌ | ❌ | use case, actor, 用例图 |

## Engine Selection

Priority (high to low):
1. **Explicit**: User specifies engine — "draw with mermaid", "用 excalidraw 画"
2. **Preference**: Previously set in conversation
3. **Availability**: draw.io MCP connected? Excalidraw MCP connected?
4. **Default**: draw.io (if MCP available) → Mermaid (always available, fallback)

## How It Works

Each engine has its own set of prompt files in `prompts/{engine}/` that define:

- **Layout algorithm** — coordinate calculation formulas, spacing rules, alignment
- **Shape mapping** — which shapes to use for different semantic elements
- **Connection rules** — edge styles, arrow types, routing preferences
- **Size calculation** — node dimensions based on text content (with CJK width correction)

The shared rules in `_shared.md` provide engine-agnostic foundations:
- Content planning principles
- Layout density selection (loose/standard/compact)
- CJK content handling
- Color palette guidelines
- Quality checklist

## Example Output

Architecture diagram generated with draw.io for [privacy-mask](https://github.com/fullstackcrew-alpha/privacy-mask):

![Privacy Mask Architecture](examples/drawio/privacy-mask-architecture.svg)

## Color Palettes

| Palette | Best For |
|---------|----------|
| **Material Blue** (default) | Architecture diagrams, general purpose |
| **Purple Dream** | Data pipelines, tech flows |
| **Forest Green** | DevOps, infrastructure |
| **Warm Sunset** | Product flows, user journeys |
| **Monochrome** | Print, formal documentation |

Each palette is available for all three engines with engine-specific configuration.

## Add Your Own Diagram Type

Add new diagram types by creating a `.md` file in the appropriate engine directory.

### Steps

1. Choose the engine: `prompts/drawio/`, `prompts/mermaid/`, or `prompts/excalidraw/`

2. Create your file (e.g., `prompts/mermaid/gantt.md`)

3. Add YAML frontmatter at the top:

```yaml
---
name: Gantt Chart
triggers: [gantt, gantt chart, project plan, timeline]
description: For drawing project plans and task timelines
---
```

4. Write the diagram-specific layout rules for that engine

5. That's it! The AI assistant will automatically discover and use your new diagram type

### Add a New Engine

1. Create `prompts/{engine-name}/` directory with `_base.md`
2. Add diagram type prompts with frontmatter
3. Add palette file in `palettes/`
4. Register in `engines.md`

## License

[MIT](LICENSE)
