---
name: drawskills
description: >-
  Professional multi-engine diagram drawing skill. Supports draw.io (via MCP),
  Mermaid (.mmd files), and Excalidraw (.excalidraw JSON). Generates high-quality
  flowcharts, architecture diagrams, sequence diagrams, ER diagrams, mind maps, and more.
  Trigger: 画图/draw diagram/画个图/generate diagram + prompts/ 目录下各类型 frontmatter 定义的触发词
license: MIT
compatibility: >-
  draw.io engine requires drawio MCP server connected.
  Mermaid engine always available (text output).
  Excalidraw engine always available (JSON output).
metadata:
  author: fullstackcrew
  version: "0.2.0"
---

# DrawSkills — 专业图表绘制技能（多引擎）

让大模型通过多种引擎绘制专业级别的技术图表。支持 draw.io、Mermaid、Excalidraw 三种引擎，每种引擎提供布局算法、样式规范和图表类型专业知识。

## When to use

- 用户要求画流程图、架构图、时序图、ER 图、用例图等
- 用户说"画个图"、"draw a diagram"、"画架构"、"flowchart"
- 用户指定引擎："用 mermaid 画"、"draw with excalidraw"
- 需要可视化系统设计、业务流程、数据模型等

## Supported engines

| 引擎       | 输出格式              | 依赖                | 特点                    |
|-----------|----------------------|--------------------|-----------------------|
| draw.io   | drawio XML → SVG/PNG | drawio MCP server  | 最丰富的图表类型，精确控制  |
| Mermaid   | `.mmd` 文本文件       | 无（始终可用）       | 语法简洁，版本控制友好     |
| Excalidraw| `.excalidraw` JSON   | 无（始终可用）       | 手绘风格，适合白板场景     |

## Quick start

```
1. 识别引擎（用户指定 > 对话偏好 > MCP 可用性 > 默认）
2. 读取 engines.md 获取引擎元数据
3. 扫描 prompts/{engine}/ 匹配图表类型
4. 读取 _shared.md + {engine}/_base.md + 类型 prompt
5. 执行引擎特定输出流程
```

### draw.io 输出流程
```
mcp__drawio__start_session()        — 启动会话
生成 drawio XML                     — 根据 prompt 规则
mcp__drawio__create_new_diagram()   — 创建图表
mcp__drawio__export_diagram()       — 导出 SVG/PNG
```

### Mermaid 输出流程
```
生成 .mmd 文本                      — 根据 prompt 规则
写入文件                            — output.mmd
可选：提示用户用 mmdc 渲染           — npx mmdc -i output.mmd -o output.svg
```

### Excalidraw 输出流程
```
生成 Excalidraw JSON                — 根据 prompt 规则
写入 .excalidraw 文件               — output.excalidraw
用户可在 excalidraw.com 打开         — 或 VS Code 插件
```

## What it does

- **多引擎支持** — draw.io / Mermaid / Excalidraw，根据场景自动或手动选择
- **智能布局引擎** — 根据节点数量自动计算坐标，告别手动算 x/y
- **可扩展图表类型** — 通过 prompts/{engine}/ 目录动态发现，只需添加 .md 文件即可支持新类型
- **专业配色方案** — 每个引擎预定义多套配色，风格统一
- **自动连线规则** — 根据图表类型选择最佳连线方式
- **中文优化** — 字体大小、间距针对中文内容优化

## Important notes

- draw.io 引擎导出 SVG 格式可自动适配内容大小，推荐使用
- Mermaid 引擎无需任何 MCP 连接，始终可用作兜底方案
- Excalidraw 输出为 JSON 文件，可在 excalidraw.com 或 VS Code 插件中编辑
- 每次编辑 draw.io 图表前先调用 `get_diagram` 获取最新状态
- 节点 ID 从 "2" 开始（0 和 1 是 drawio 保留的）
