# 引擎注册表

## 可用引擎

### drawio

- **ID**: `drawio`
- **名称**: Draw.io
- **输出格式**: drawio XML → SVG/PNG
- **依赖**: drawio MCP server (`mcp__drawio__*`)
- **特点**: 最丰富的图表类型支持，精确坐标控制，专业导出
- **支持的图表类型**: flowchart, architecture, sequence, er-diagram, mindmap, usecase, api-call
- **配色文件**: `palettes/colors.md`
- **输出流程**:
  1. `mcp__drawio__start_session()` — 启动会话
  2. `mcp__drawio__create_new_diagram(xml)` — 创建图表
  3. `mcp__drawio__export_diagram(format)` — 导出 SVG/PNG

### mermaid

- **ID**: `mermaid`
- **名称**: Mermaid
- **输出格式**: `.mmd` 文本文件
- **依赖**: 无（纯文本输出，始终可用）
- **特点**: 语法简洁、版本控制友好、无需 MCP、可嵌入 Markdown
- **支持的图表类型**: flowchart, sequence, er-diagram, mindmap, api-call
- **配色文件**: `palettes/mermaid-themes.md`
- **输出流程**:
  1. 生成 `.mmd` 文件内容
  2. 写入文件（如 `output.mmd`）
  3. 可选：提示用户用 `mmdc` CLI 渲染为 SVG/PNG

### excalidraw

- **ID**: `excalidraw`
- **名称**: Excalidraw
- **输出格式**: `.excalidraw` JSON 文件
- **依赖**: 无（直接写 JSON 文件）或 excalidraw MCP server
- **特点**: 手绘风格、轻量级、适合白板/头脑风暴场景
- **支持的图表类型**: flowchart, architecture, api-call
- **配色文件**: `palettes/excalidraw-colors.md`
- **输出流程**:
  1. 生成 Excalidraw JSON 结构
  2. 写入 `.excalidraw` 文件
  3. 用户可在 excalidraw.com 或 VS Code 插件中打开

## 引擎选择规则

优先级从高到低：

1. **用户明确指定** — "用 mermaid 画" / "draw with excalidraw" / "用 drawio"
2. **对话中已有偏好** — 用户之前设定过引擎偏好
3. **引擎可用性** — drawio MCP 是否连接、excalidraw MCP 是否连接
4. **默认顺序** — drawio（如 MCP 可用）→ mermaid（始终可用，兜底）

### 引擎指定的触发词

| 引擎       | 触发词                                                    |
|-----------|----------------------------------------------------------|
| drawio    | drawio, draw.io, 用drawio                                |
| mermaid   | mermaid, 用mermaid, mmd                                   |
| excalidraw| excalidraw, 用excalidraw, 手绘风格, hand-drawn, whiteboard |

### 图表类型与引擎兼容性

| 图表类型      | drawio | mermaid | excalidraw |
|--------------|--------|---------|------------|
| flowchart    | ✅     | ✅      | ✅         |
| architecture | ✅     | ❌      | ✅         |
| sequence     | ✅     | ✅      | ❌         |
| er-diagram   | ✅     | ✅      | ❌         |
| mindmap      | ✅     | ✅      | ❌         |
| usecase      | ✅     | ❌      | ❌         |
| api-call     | ✅     | ✅      | ✅         |

当用户请求的图表类型在当前引擎不支持时，建议切换到支持该类型的引擎。
