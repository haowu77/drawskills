# DrawSkills — 专业图表绘制（多引擎）

当用户要求画图时，选择合适的绘图引擎，动态扫描对应引擎的 `prompts/` 目录发现可用的图表类型，加载匹配的 prompt 模板，遵循其中的布局规则和样式规范。

## 项目结构

```
drawskills/
├── CLAUDE.md                       # 项目说明（本文件）
├── SKILL.md                        # agentskills.io 技能定义
├── engines.md                      # 引擎注册表 + 选择规则
├── prompts/
│   ├── _shared.md                  # 引擎无关的通用规则
│   ├── drawio/
│   │   ├── _base.md               # drawio XML 基础规则
│   │   ├── flowchart.md           # 各图表类型专用规则
│   │   ├── architecture.md
│   │   ├── sequence.md
│   │   ├── er-diagram.md
│   │   ├── mindmap.md
│   │   └── usecase.md
│   ├── mermaid/
│   │   ├── _base.md               # Mermaid 语法基础规则
│   │   ├── flowchart.md
│   │   ├── sequence.md
│   │   ├── er-diagram.md
│   │   └── mindmap.md
│   └── excalidraw/
│       ├── _base.md               # Excalidraw JSON 基础规则
│       ├── flowchart.md
│       └── architecture.md
├── palettes/
│   ├── colors.md                  # drawio 配色方案
│   ├── mermaid-themes.md          # Mermaid 主题配置
│   └── excalidraw-colors.md       # Excalidraw 颜色属性
└── examples/
    ├── drawio/                    # drawio 示例
    ├── mermaid/                   # Mermaid 示例
    └── excalidraw/                # Excalidraw 示例
```

## 工作流程

1. 用户说"画个 XXX 图" → 识别绘图意图
2. **确定引擎**（按优先级）：
   - 用户明确指定："用 mermaid 画" / "draw with excalidraw"
   - 对话中已有偏好设定
   - 引擎可用性：drawio MCP 是否连接、excalidraw MCP 是否连接
   - 默认：drawio（如 MCP 可用）→ mermaid（始终可用，兜底）
3. 读取 `engines.md` 获取引擎元数据和能力矩阵
4. 扫描 `prompts/{engine}/` 目录下所有 `.md` 文件（排除 `_base.md`），读取每个文件的 frontmatter 中的 `triggers` 字段
5. 将用户输入与各文件的 `triggers` 关键词匹配，选择最佳匹配的 prompt 文件
6. 若当前引擎不支持该图表类型 → 建议用户切换到支持的引擎
7. 读取 `prompts/_shared.md` + `prompts/{engine}/_base.md` + 匹配的类型 prompt 文件
8. 读取对应引擎的配色方案文件
9. 执行引擎特定输出流程：
   - **drawio**: `mcp__drawio__start_session()` → 生成 XML → `mcp__drawio__create_new_diagram()` → `mcp__drawio__export_diagram()`
   - **mermaid**: 生成 `.mmd` 文件内容 → 写入文件 → 可选提示用户用 `mmdc` 渲染
   - **excalidraw**: 生成 Excalidraw JSON → 写入 `.excalidraw` 文件

## Prompt 文件规范

每个图表类型的 prompt 文件必须在开头包含 YAML frontmatter：

```yaml
---
name: 图表类型名称
triggers: [关键词1, keyword2, ...]
description: 简短描述该图表类型的用途
---
```

- `name`：图表类型的显示名称
- `triggers`：触发关键词列表，用于匹配用户意图（支持中英文）
- `description`：该图表类型的简短说明

`_base.md` 和 `_shared.md` 不需要 frontmatter，总是自动加载。

## 引擎选择触发词

| 引擎       | 触发词                                                    |
|-----------|----------------------------------------------------------|
| drawio    | drawio, draw.io, 用drawio                                |
| mermaid   | mermaid, 用mermaid, mmd                                   |
| excalidraw| excalidraw, 用excalidraw, 手绘风格, hand-drawn, whiteboard |

## 关键约定

- **必须先读 prompt 再画图** — prompt 文件包含布局算法和样式规则，不要凭感觉画
- **动态发现图表类型** — 不要硬编码图表类型列表，始终通过扫描 `prompts/{engine}/` 目录发现
- **引擎不支持时要提示** — 如果用户选的引擎不支持请求的图表类型，主动建议切换
- **优先 SVG 导出**（drawio）— PNG 可能有空白问题，SVG 自动适配内容
- **中文内容加宽** — 中文字符比英文宽，节点宽度至少 ×1.3
- **节点数量决定布局** — 少于 8 个用宽松布局，8-15 用标准布局，15+ 用紧凑布局
- **每张图一个主题** — 不要把太多信息塞进一张图，宁可拆成多张
