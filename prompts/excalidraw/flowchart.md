---
name: 流程图
triggers: [流程图, flowchart, flow, 流程, process]
description: 用于绘制业务流程、操作步骤、决策分支（Excalidraw 版）
---

# Excalidraw 流程图绘制规则

## 布局方向

**默认：从上到下（Top-Down）**

主流程节点居中对齐，分支向两侧展开。

## 节点形状映射

| 含义       | Excalidraw type | 特殊处理                      |
|-----------|----------------|-------------------------------|
| 开始/结束  | `ellipse`      | 宽高比约 2.5:1                 |
| 处理步骤   | `rectangle`    | `roundness: { type: 3 }`      |
| 判断/条件  | `diamond`      | 宽高各 120px                   |
| 数据存储   | `rectangle`    | 圆角 + 特殊颜色标识             |

## 布局算法

```python
center_x = 400                    # 主流程中心 x
current_y = 60                    # 起始 y
v_gap = 120                       # 垂直间距
node_width = 200
node_height = 80

for step in steps:
    if step.type == "decision":
        # 菱形节点
        node = diamond(
            x=center_x - 60,
            y=current_y,
            width=120, height=120
        )
        # Yes 分支向右
        yes_x = center_x + 200
        # No 分支向下继续主流程
    else:
        node = rectangle(
            x=center_x - node_width/2,
            y=current_y,
            width=node_width,
            height=node_height
        )
    current_y += node_height + v_gap
```

## 元素生成示例

### 处理步骤节点

```json
{
  "id": "node-validate",
  "type": "rectangle",
  "x": 300,
  "y": 200,
  "width": 200,
  "height": 80,
  "strokeColor": "#455A64",
  "backgroundColor": "#ffffff",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "roughness": 1,
  "roundness": { "type": 3 },
  "boundElements": [
    { "id": "text-validate", "type": "text" },
    { "id": "arrow-1", "type": "arrow" }
  ]
}
```

### 节点内文字

```json
{
  "id": "text-validate",
  "type": "text",
  "x": 350,
  "y": 225,
  "width": 100,
  "height": 30,
  "text": "验证数据",
  "fontSize": 16,
  "fontFamily": 2,
  "textAlign": "center",
  "verticalAlign": "middle",
  "containerId": "node-validate"
}
```

### 连线箭头

```json
{
  "id": "arrow-1",
  "type": "arrow",
  "x": 400,
  "y": 280,
  "width": 0,
  "height": 40,
  "points": [[0, 0], [0, 40]],
  "startBinding": {
    "elementId": "node-validate",
    "focus": 0,
    "gap": 5
  },
  "endBinding": {
    "elementId": "node-next",
    "focus": 0,
    "gap": 5
  },
  "strokeColor": "#78909C",
  "strokeWidth": 2,
  "endArrowhead": "arrow"
}
```

## 判断分支

菱形节点的两个分支：

- **是（Yes）**：箭头向右，标签 "是"
- **否（No）**：箭头向下，标签 "否"

分支标签用独立 text 元素放在箭头旁边。

## 样式配色

| 含义       | backgroundColor | strokeColor |
|-----------|----------------|-------------|
| 开始/结束  | `#E8F5E9`      | `#43A047`   |
| 处理步骤   | `#ffffff`      | `#455A64`   |
| 判断/条件  | `#FFF8E1`      | `#FFA000`   |
| 数据存储   | `#FCE4EC`      | `#C62828`   |
| 连线       | transparent    | `#78909C`   |

## 分阶段分组

用大矩形（浅色背景）包裹同阶段的节点，设置相同的 `groupIds`：

```json
{
  "id": "phase-1-bg",
  "type": "rectangle",
  "backgroundColor": "#F5F5F5",
  "strokeColor": "#E0E0E0",
  "fillStyle": "solid",
  "opacity": 50,
  "roundness": { "type": 3 }
}
```
