---
name: 架构图
triggers: [架构图, architecture, arch, 架构, system design, 系统设计]
description: 用于绘制系统架构、分层结构、组件关系（Excalidraw 版）
---

# Excalidraw 架构图绘制规则

## 布局模式

使用**分层布局（Layered Layout）**，从上到下分层。每层是一个大背景矩形 + 层内组件。

```
┌─────────────── 用户层 ───────────────┐
│  浏览器    移动端 App    第三方系统     │
└──────────────────────────────────────┘
                   ↓
┌─────────────── 服务层 ───────────────┐
│  服务A    服务B    服务C              │
└──────────────────────────────────────┘
                   ↓
┌─────────────── 数据层 ───────────────┐
│  MySQL    Redis    ElasticSearch     │
└──────────────────────────────────────┘
```

## 布局算法

```python
margin_left = 60
margin_top = 80
layer_gap = 50                    # 层间距
component_gap = 40                # 组件间距
padding = 30                      # 层内边距
component_height = 70
component_width = 160

# 计算层宽度
max_components = max(len(layer.components) for layer in layers)
layer_width = padding * 2 + max_components * component_width + (max_components - 1) * component_gap

current_y = margin_top + 60       # 标题下方

for layer in layers:
    layer_height = component_height + padding * 2 + 30  # 30 for label

    # 层背景矩形
    layer_bg = rectangle(
        x=margin_left, y=current_y,
        width=layer_width, height=layer_height
    )

    # 层标签
    label = text(
        x=margin_left + 15, y=current_y + 10,
        text=layer.name
    )

    # 层内组件水平排列
    comp_start_x = margin_left + padding
    comp_y = current_y + 40
    for i, comp in enumerate(layer.components):
        comp.x = comp_start_x + i * (component_width + component_gap)
        comp.y = comp_y

    current_y += layer_height + layer_gap
```

## 层背景元素

```json
{
  "id": "layer-service",
  "type": "rectangle",
  "x": 60,
  "y": 200,
  "width": 800,
  "height": 130,
  "strokeColor": "#E0E0E0",
  "backgroundColor": "#F5F5F5",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "roughness": 0,
  "opacity": 60,
  "roundness": { "type": 3 }
}
```

## 组件元素

```json
{
  "id": "comp-api-gateway",
  "type": "rectangle",
  "x": 90,
  "y": 240,
  "width": 160,
  "height": 70,
  "strokeColor": "#1976D2",
  "backgroundColor": "#E3F2FD",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "roughness": 1,
  "roundness": { "type": 3 },
  "boundElements": [
    { "id": "text-api-gateway", "type": "text" }
  ]
}
```

## 组件形状映射

| 组件类型     | type        | 视觉区分方式                        |
|-------------|-------------|-------------------------------------|
| 服务/应用    | `rectangle` | 蓝色系：`#E3F2FD` / `#1976D2`       |
| 数据库       | `rectangle` | 橙色系：`#FFF3E0` / `#EF6C00` + 圆角 |
| 消息队列     | `rectangle` | 紫色系：`#F3E5F5` / `#7B1FA2`       |
| 缓存        | `rectangle` | 红色系：`#FFEBEE` / `#C62828`        |
| 外部系统     | `rectangle` | 灰色虚线：`strokeStyle: "dashed"`    |
| 用户/客户端  | `ellipse`   | 绿色系：`#E8F5E9` / `#43A047`       |

## 层间连线

```json
{
  "id": "arrow-layer1-to-layer2",
  "type": "arrow",
  "strokeColor": "#BDBDBD",
  "strokeWidth": 2,
  "roughness": 1,
  "endArrowhead": "arrow",
  "startBinding": { "elementId": "comp-source", "focus": 0, "gap": 5 },
  "endBinding": { "elementId": "comp-target", "focus": 0, "gap": 5 }
}
```

### 连线标签

用独立 text 元素放在箭头中点旁边：

```json
{
  "id": "label-protocol",
  "type": "text",
  "text": "HTTP/REST",
  "fontSize": 12,
  "fontFamily": 2,
  "fillStyle": "solid",
  "backgroundColor": "#ffffff"
}
```

## 标题

```json
{
  "id": "title",
  "type": "text",
  "x": 300,
  "y": 20,
  "text": "系统架构图",
  "fontSize": 24,
  "fontFamily": 2,
  "textAlign": "center"
}
```

## 复杂度控制

- 每层最多 5-6 个组件
- 总层数 3-5 层
- 手绘风格适合概念架构，正式架构建议 `roughness: 0`
- 连线交叉超过 3 处时调整组件位置
