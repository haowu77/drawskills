---
name: API 调用图
triggers: [API调用, api call, REST, RESTful, 接口调用, api flow, 接口, endpoint, HTTP]
description: 用于展示客户端如何调用 RESTful API 的请求/响应流程（Excalidraw 版）
---

# Excalidraw API 调用图绘制规则

## 布局结构

左侧客户端 + 右侧服务端，中间用箭头和信息卡片展示请求/响应。

```
[Client]                                    [Server]
   │                                           │
   │  ┌─ POST /api/users ──────────────┐      │
   │  │ { "name": "Alice" }            │      │
   │──┤                                ├─────→│
   │  └────────────────────────────────┘      │
   │                                           │
   │  ┌─ 201 Created ─────────────────┐      │
   │  │ { "id": 1, "name": "Alice" }  │      │
   │←─┤                                ├──────│
   │  └────────────────────────────────┘      │
```

## 参与者元素

### 客户端

```json
{
  "id": "node-client",
  "type": "rectangle",
  "x": 40,
  "y": 60,
  "width": 160,
  "height": 70,
  "strokeColor": "#43A047",
  "backgroundColor": "#E8F5E9",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "roughness": 1,
  "roundness": { "type": 3 },
  "boundElements": [
    { "id": "text-client", "type": "text" }
  ]
},
{
  "id": "text-client",
  "type": "text",
  "text": "Client\n(Web / Mobile)",
  "fontSize": 16,
  "fontFamily": 2,
  "textAlign": "center",
  "verticalAlign": "middle",
  "containerId": "node-client"
}
```

### 服务端

```json
{
  "id": "node-server",
  "type": "rectangle",
  "x": 600,
  "y": 60,
  "width": 160,
  "height": 70,
  "strokeColor": "#1976D2",
  "backgroundColor": "#E3F2FD",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "roughness": 1,
  "roundness": { "type": 3 },
  "boundElements": [
    { "id": "text-server", "type": "text" }
  ]
}
```

## 生命线

用 `line` 元素画虚线：

```json
{
  "id": "lifeline-client",
  "type": "line",
  "x": 120,
  "y": 130,
  "width": 0,
  "height": 600,
  "points": [[0, 0], [0, 600]],
  "strokeColor": "#BDBDBD",
  "strokeWidth": 1,
  "strokeStyle": "dashed"
}
```

## 请求信息卡片

每个 API 请求用一个圆角矩形卡片 + 内部文字：

```json
{
  "id": "card-req-1",
  "type": "rectangle",
  "x": 180,
  "y": 160,
  "width": 300,
  "height": 80,
  "strokeColor": "#1976D2",
  "backgroundColor": "#E3F2FD",
  "fillStyle": "solid",
  "strokeWidth": 1,
  "roughness": 0,
  "roundness": { "type": 3 },
  "boundElements": [
    { "id": "text-req-1", "type": "text" }
  ]
},
{
  "id": "text-req-1",
  "type": "text",
  "text": "POST /api/users\n{ \"name\": \"Alice\" }",
  "fontSize": 14,
  "fontFamily": 3,
  "textAlign": "left",
  "verticalAlign": "middle",
  "containerId": "card-req-1"
}
```

## 请求箭头

```json
{
  "id": "arrow-req-1",
  "type": "arrow",
  "x": 120,
  "y": 200,
  "width": 480,
  "height": 0,
  "points": [[0, 0], [480, 0]],
  "strokeColor": "#1976D2",
  "strokeWidth": 2,
  "roughness": 1,
  "startArrowhead": null,
  "endArrowhead": "arrow",
  "startBinding": null,
  "endBinding": null
}
```

## 响应信息卡片

```json
{
  "id": "card-res-1",
  "type": "rectangle",
  "x": 180,
  "y": 270,
  "width": 300,
  "height": 70,
  "strokeColor": "#43A047",
  "backgroundColor": "#E8F5E9",
  "fillStyle": "solid",
  "strokeWidth": 1,
  "roughness": 0,
  "roundness": { "type": 3 },
  "boundElements": [
    { "id": "text-res-1", "type": "text" }
  ]
},
{
  "id": "text-res-1",
  "type": "text",
  "text": "201 Created\n{ \"id\": 1, \"name\": \"Alice\" }",
  "fontSize": 14,
  "fontFamily": 3,
  "textAlign": "left",
  "verticalAlign": "middle",
  "containerId": "card-res-1"
}
```

## 响应箭头（反方向虚线）

```json
{
  "id": "arrow-res-1",
  "type": "arrow",
  "x": 600,
  "y": 305,
  "width": 480,
  "height": 0,
  "points": [[0, 0], [-480, 0]],
  "strokeColor": "#78909C",
  "strokeWidth": 1,
  "strokeStyle": "dashed",
  "roughness": 1,
  "startArrowhead": null,
  "endArrowhead": "arrow"
}
```

## HTTP 方法颜色编码

| 方法    | 卡片 backgroundColor | 卡片 strokeColor | 箭头 strokeColor |
|--------|---------------------|-----------------|-----------------|
| GET    | `#E3F2FD`           | `#1976D2`       | `#1976D2`       |
| POST   | `#E8F5E9`           | `#43A047`       | `#43A047`       |
| PUT    | `#FFF3E0`           | `#EF6C00`       | `#EF6C00`       |
| PATCH  | `#FFF8E1`           | `#FFA000`       | `#FFA000`       |
| DELETE | `#FFEBEE`           | `#E53935`       | `#E53935`       |

## 响应状态码颜色

| 状态码范围 | backgroundColor | strokeColor |
|-----------|----------------|-------------|
| 2xx 成功   | `#E8F5E9`      | `#43A047`   |
| 4xx 客户端错误 | `#FFF8E1`  | `#FFA000`   |
| 5xx 服务端错误 | `#FFEBEE`  | `#E53935`   |

## 布局算法

```python
client_x = 40
server_x = 600
card_x = 180
card_width = 300

current_y = 160
card_gap = 20          # 请求卡片与响应卡片间距
group_gap = 50         # 调用组之间间距
card_height_base = 70
line_height = 18

for call in api_calls:
    # 请求卡片
    req_lines = count_lines(call.method + " " + call.path + "\n" + call.body)
    req_h = max(card_height_base, 20 + req_lines * line_height)
    # draw request card at (card_x, current_y, card_width, req_h)
    # draw request arrow at y = current_y + req_h / 2
    current_y += req_h + card_gap

    # 响应卡片
    res_lines = count_lines(call.status + "\n" + call.response_body)
    res_h = max(card_height_base, 20 + res_lines * line_height)
    # draw response card at (card_x, current_y, card_width, res_h)
    # draw response arrow at y = current_y + res_h / 2
    current_y += res_h + group_gap
```

## 场景分组

用半透明大矩形包裹同一场景的调用：

```json
{
  "id": "group-bg-auth",
  "type": "rectangle",
  "x": 30,
  "y": 150,
  "width": 740,
  "height": 250,
  "strokeColor": "#78909C",
  "backgroundColor": "#F5F5F5",
  "fillStyle": "solid",
  "strokeWidth": 1,
  "strokeStyle": "dashed",
  "roughness": 0,
  "opacity": 40,
  "roundness": { "type": 3 }
}
```

场景标签用独立 text 元素放在左上角。

## 复杂度控制

- 单张图最多 6 个 API 调用
- 参与者最多 3 个（Client + Server + 可选 DB）
- 卡片内容最多 3 行
- 使用 `fontFamily: 3`（等宽字体）展示路径和 JSON
- 超过则按场景拆分
