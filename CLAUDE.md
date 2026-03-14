# DrawSkills — 专业图表绘制

当用户要求画图时，加载 `prompts/` 目录下对应图表类型的 prompt 模板，遵循其中的布局规则和样式规范。

## 项目结构

```
drawskills/
├── CLAUDE.md              # 项目说明（本文件）
├── SKILL.md               # agentskills.io 技能定义
├── prompts/
│   ├── _base.md           # 通用基础规则（所有图表类型共享）
│   ├── flowchart.md       # 流程图专用规则
│   ├── architecture.md    # 架构图专用规则
│   ├── sequence.md        # 时序图专用规则
│   ├── er-diagram.md      # ER 图专用规则
│   ├── mindmap.md         # 思维导图专用规则
│   └── usecase.md         # 用例图专用规则
├── palettes/
│   └── colors.md          # 配色方案定义
└── examples/
    └── *.drawio           # 各类型示例文件
```

## 工作流程

1. 用户说"画个 XXX 图" → 识别图表类型
2. 读取 `prompts/_base.md` + 对应类型的 prompt 文件
3. 读取 `palettes/colors.md` 选择配色方案
4. 调用 `mcp__drawio__start_session()` 启动会话
5. 根据 prompt 规则生成 drawio XML
6. 调用 `mcp__drawio__create_new_diagram()` 创建图表
7. 导出为 SVG（推荐）或 PNG

## 关键约定

- **必须先读 prompt 再画图** — prompt 文件包含布局算法和样式规则，不要凭感觉画
- **优先 SVG 导出** — PNG 可能有空白问题，SVG 自动适配内容
- **中文内容加宽** — 中文字符比英文宽，节点宽度至少 ×1.3
- **节点数量决定布局** — 少于 8 个用宽松布局，8-15 用标准布局，15+ 用紧凑布局
- **每张图一个主题** — 不要把太多信息塞进一张图，宁可拆成多张
