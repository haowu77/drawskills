---
name: drawskills
description: >-
  Professional diagram drawing skill for drawio MCP. Generates high-quality
  flowcharts, architecture diagrams, sequence diagrams, ER diagrams, and more.
  Trigger: 画图/draw diagram/flowchart/架构图/时序图/流程图/ER图/sequence diagram
license: MIT
compatibility: Requires drawio MCP server connected
metadata:
  author: fullstackcrew
  version: "0.1.0"
---

# DrawSkills — 专业图表绘制技能

让大模型通过 drawio MCP 绘制专业级别的技术图表。不只是通道，更提供布局算法、样式规范和图表类型专业知识。

## When to use

- 用户要求画流程图、架构图、时序图、ER 图、用例图等
- 用户说"画个图"、"draw a diagram"、"画架构"、"flowchart"
- 需要可视化系统设计、业务流程、数据模型等

## Quick start

```
1. mcp__drawio__start_session()        — 启动会话
2. 根据图表类型选择对应模板和布局规则
3. mcp__drawio__create_new_diagram()   — 生成图表
4. mcp__drawio__export_diagram()       — 导出 SVG/PNG
```

## What it does

- **智能布局引擎** — 根据节点数量自动计算坐标，告别手动算 x/y
- **6 种图表类型** — 流程图、架构图、时序图、ER 图、思维导图、用例图
- **专业配色方案** — 预定义多套配色，风格统一
- **自动连线规则** — 根据图表类型选择最佳连线方式
- **中文优化** — 字体大小、间距针对中文内容优化

## Important notes

- 导出 SVG 格式可自动适配内容大小，推荐使用
- PNG 导出可能包含多余空白，SVG 更可靠
- 每次编辑前先调用 `get_diagram` 获取最新状态
- 节点 ID 从 "2" 开始（0 和 1 是 drawio 保留的）
