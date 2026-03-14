# DrawSkills

> 通过多种引擎为 AI 编程助手提供专业图表绘制能力。支持 [draw.io MCP](https://github.com/agentic-labs/drawio-mcp)、Mermaid、Excalidraw。

[English](README.md)

让 AI 编程助手通过多种渲染引擎绘制**专业级别**的技术图表。不只是简单的工具调用通道，更为每种引擎提供完整的布局算法、样式规范和图表类型专业知识。

## 特性

- **多引擎支持** — draw.io（通过 MCP）、Mermaid（.mmd）、Excalidraw（.excalidraw JSON）
- **可扩展图表类型** — 内置流程图、架构图、时序图、ER 图、思维导图、用例图，支持自定义扩展
- **智能布局引擎** — 根据节点数量自动计算坐标和间距，告别手动算 x/y
- **专业配色方案** — 每种引擎预定义 5 套配色 (Material Blue / Purple Dream / Forest Green / Warm Sunset / Monochrome)
- **自动连线规则** — 根据图表类型选择最佳连线方式和出入点
- **中文优化** — 字体大小、节点宽度、间距针对中文内容专门调优

## 支持的引擎

| 引擎       | 输出格式              | 依赖               | 适用场景                |
|-----------|----------------------|-------------------|-----------------------|
| **draw.io** | drawio XML → SVG/PNG | drawio MCP server | 丰富图表类型，精确布局控制  |
| **Mermaid** | `.mmd` 文本文件       | 无（始终可用）      | 快速绘图，版本控制友好     |
| **Excalidraw** | `.excalidraw` JSON | 无（始终可用）      | 手绘风格，白板场景        |

## 快速开始

### 1. 前置条件

- 支持 [Agent Skills](https://agentskills.io) 的 AI 编程助手（如 Claude Code）
- 可选：[drawio MCP server](https://github.com/agentic-labs/drawio-mcp) 用于 draw.io 引擎

### 2. 安装

克隆仓库并作为技能目录添加：

```bash
git clone https://github.com/haowu77/drawskills.git
```

### 3. 使用

告诉你的 AI 助手：

```
画一个用户登录的流程图
```

```
用 mermaid 画个时序图
```

```
用 excalidraw 画个架构图
```

技能会自动：
1. 选择最佳引擎（用户指定 > MCP 可用性 > 默认）
2. 扫描 `prompts/{engine}/` 目录发现可用的图表类型
3. 通过触发关键词匹配用户意图
4. 加载对应的布局规则和样式模板
5. 选择合适的配色方案
6. 以引擎原生格式生成输出

## 项目结构

```
drawskills/
├── CLAUDE.md                       # AI 助手项目指令
├── SKILL.md                        # AgentSkills.io 技能定义
├── engines.md                      # 引擎注册表 + 选择规则
├── prompts/
│   ├── _shared.md                  # 引擎无关的通用规则
│   ├── drawio/                     # draw.io 引擎 prompts
│   │   ├── _base.md               # XML 结构、布局、样式
│   │   └── *.md                   # 各图表类型规则
│   ├── mermaid/                    # Mermaid 引擎 prompts
│   │   ├── _base.md               # Mermaid 语法基础
│   │   └── *.md                   # 各图表类型规则
│   └── excalidraw/                 # Excalidraw 引擎 prompts
│       ├── _base.md               # JSON 结构、元素类型
│       └── *.md                   # 各图表类型规则
├── palettes/
│   ├── colors.md                  # draw.io 配色方案
│   ├── mermaid-themes.md          # Mermaid 主题配置
│   └── excalidraw-colors.md       # Excalidraw 颜色属性
└── examples/
    ├── drawio/                    # draw.io 示例输出
    ├── mermaid/                   # Mermaid 示例输出
    └── excalidraw/                # Excalidraw 示例输出
```

## 内置图表类型

| 类型     | draw.io | Mermaid | Excalidraw | 触发关键词 |
|---------|---------|---------|------------|-----------|
| 流程图   | ✅      | ✅      | ✅         | 流程图, flowchart, flow, 流程, process |
| 架构图   | ✅      | ❌      | ✅         | 架构图, architecture, arch, 系统设计 |
| 时序图   | ✅      | ✅      | ❌         | 时序图, sequence, 交互图 |
| ER 图   | ✅      | ✅      | ❌         | ER图, 实体关系, 数据模型 |
| 思维导图 | ✅      | ✅      | ❌         | 思维导图, mindmap, 脑图 |
| 用例图   | ✅      | ❌      | ❌         | 用例图, use case, 用例 |

## 引擎选择

优先级（从高到低）：
1. **用户明确指定**："用 mermaid 画" / "draw with excalidraw"
2. **对话偏好**：之前在对话中设定过
3. **可用性检查**：drawio MCP 是否连接？
4. **默认**：drawio（如 MCP 可用）→ mermaid（始终可用，兜底）

## 工作原理

每种引擎在 `prompts/{engine}/` 下都有自己的 prompt 集合，定义了：

- **布局算法** — 坐标计算公式、间距规则、对齐方式
- **形状映射** — 不同语义元素对应的形状
- **连线规则** — 边的样式、箭头类型、路由偏好
- **尺寸计算** — 基于文本内容的节点尺寸（含中文宽度校正）

`_shared.md` 中的通用规则提供引擎无关的基础：
- 内容规划原则
- 布局密度选择（宽松/标准/紧凑）
- 中文内容处理
- 配色原则
- 质量检查清单

## 示例输出

用 draw.io 为 [privacy-mask](https://github.com/fullstackcrew-alpha/privacy-mask) 生成的架构图：

![Privacy Mask Architecture](examples/drawio/privacy-mask-architecture.svg)

## 配色方案

| 方案 | 适用场景 |
|------|---------|
| **Material Blue**（默认） | 架构图、通用 |
| **Purple Dream** | 数据管道、技术流程 |
| **Forest Green** | DevOps、基础设施 |
| **Warm Sunset** | 产品流程、用户旅程 |
| **Monochrome** | 打印、正式文档 |

每种配色方案在三个引擎中都有对应的配置。

## 添加自定义图表类型

在对应引擎目录下创建 `.md` 文件即可添加新图表类型。

### 步骤

1. 选择引擎：`prompts/drawio/`、`prompts/mermaid/` 或 `prompts/excalidraw/`

2. 创建文件（例如 `prompts/mermaid/gantt.md`）

3. 在文件头部添加 YAML frontmatter：

```yaml
---
name: 甘特图
triggers: [甘特图, gantt, gantt chart, 项目计划, project plan]
description: 用于绘制项目计划、任务时间线
---
```

4. 参照该引擎的 `_base.md` 编写图表专用的布局规则

5. 完成！AI 助手会自动发现并使用你的新图表类型

### 添加新引擎

1. 创建 `prompts/{engine-name}/` 目录，包含 `_base.md`
2. 添加带 frontmatter 的图表类型 prompt
3. 在 `palettes/` 下添加配色文件
4. 在 `engines.md` 中注册

## 许可证

[MIT](LICENSE)
