---
name: API 调用图
triggers: [API调用, api call, REST, RESTful, 接口调用, api flow, 接口, endpoint, HTTP]
description: 用于展示客户端如何调用 RESTful API 的请求/响应流程
---

# API 调用图绘制规则

## 核心概念

API 调用图是时序图的垂直变体，专注于展示客户端与服务端之间的 HTTP 请求/响应交互。每个调用包含：方法、路径、请求体、响应状态码、响应体。

```
┌──────────┐                        ┌──────────┐
│  Client  │                        │  Server  │
└────┬─────┘                        └────┬─────┘
     │                                   │
     │  POST /api/users                  │
     │  { "name": "Alice" }              │
     │──────────────────────────────────→│
     │                                   │
     │  201 Created                      │
     │  { "id": 1, "name": "Alice" }     │
     │←──────────────────────────────────│
     │                                   │
```

## 布局结构

### 整体布局

```
┌─────────────────────────────────────────────────┐
│                   图表标题                        │
├─────────────────────────────────────────────────┤
│                                                  │
│  [Client]              [Server / Service]        │
│     │                       │                    │
│     │─── 请求卡片 ─────────→│                    │
│     │                       │                    │
│     │←── 响应卡片 ──────────│                    │
│     │                       │                    │
│     │─── 请求卡片 ─────────→│                    │
│     │                       │                    │
│     │←── 响应卡片 ──────────│                    │
│                                                  │
└─────────────────────────────────────────────────┘
```

多服务时参与者水平排列（同时序图），但每条消息附带详细的请求/响应信息卡片。

## 参与者样式

```xml
<!-- 客户端 -->
<mxCell id="client" value="Client&#xa;(Web / Mobile)"
  style="rounded=1;fillColor=#E8F5E9;strokeColor=#43A047;strokeWidth=2;
         fontSize=14;fontStyle=1;fontColor=#1B5E20;whiteSpace=wrap;align=center;"
  vertex="1" parent="1">
  <mxGeometry x="60" y="80" width="140" height="60" as="geometry"/>
</mxCell>

<!-- 服务端 -->
<mxCell id="server" value="API Server&#xa;(REST)"
  style="rounded=1;fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=2;
         fontSize=14;fontStyle=1;fontColor=#0D47A1;whiteSpace=wrap;align=center;"
  vertex="1" parent="1">
  <mxGeometry x="500" y="80" width="140" height="60" as="geometry"/>
</mxCell>

<!-- 数据库（可选第三列） -->
<mxCell id="database" value="Database"
  style="shape=cylinder3;fillColor=#FFF3E0;strokeColor=#EF6C00;strokeWidth=2;
         fontSize=14;fontStyle=1;fontColor=#BF360C;size=12;"
  vertex="1" parent="1">
  <mxGeometry x="800" y="80" width="100" height="70" as="geometry"/>
</mxCell>
```

## 生命线

同时序图规则，每个参与者下方画虚线：

```xml
<mxCell id="lifeline_client"
  style="endArrow=none;strokeWidth=1;strokeColor=#BDBDBD;dashed=1;dashPattern=5 5;"
  edge="1" parent="1">
  <mxGeometry relative="1" as="geometry">
    <mxPoint x="CLIENT_CENTER_X" y="PARTICIPANT_BOTTOM" as="sourcePoint"/>
    <mxPoint x="CLIENT_CENTER_X" y="DIAGRAM_BOTTOM" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

## 请求箭头 + 信息卡片

每个 API 调用由一个请求箭头和一个**请求信息卡片**组成：

```xml
<!-- 请求箭头 -->
<mxCell id="req1_arrow" value=""
  style="endArrow=block;endFill=1;strokeWidth=2;strokeColor=#1976D2;"
  edge="1" parent="1">
  <mxGeometry relative="1" as="geometry">
    <mxPoint x="CLIENT_CENTER" y="MSG_Y" as="sourcePoint"/>
    <mxPoint x="SERVER_CENTER" y="MSG_Y" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- 请求信息卡片（箭头上方） -->
<mxCell id="req1_card" value="POST /api/users&#xa;Content-Type: application/json&#xa;&#xa;{ &quot;name&quot;: &quot;Alice&quot;, &quot;email&quot;: &quot;alice@example.com&quot; }"
  style="rounded=1;fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=1;
         fontSize=11;fontColor=#0D47A1;align=left;verticalAlign=top;
         spacingLeft=8;spacingTop=6;spacingRight=8;spacingBottom=6;
         whiteSpace=wrap;fontFamily=Courier New;"
  vertex="1" parent="1">
  <mxGeometry x="CARD_X" y="CARD_Y" width="280" height="CARD_HEIGHT" as="geometry"/>
</mxCell>
```

## 响应箭头 + 信息卡片

```xml
<!-- 响应箭头（虚线，反方向） -->
<mxCell id="res1_arrow" value=""
  style="endArrow=open;dashed=1;strokeWidth=1;strokeColor=#78909C;"
  edge="1" parent="1">
  <mxGeometry relative="1" as="geometry">
    <mxPoint x="SERVER_CENTER" y="MSG_Y" as="sourcePoint"/>
    <mxPoint x="CLIENT_CENTER" y="MSG_Y" as="targetPoint"/>
  </mxGeometry>
</mxCell>

<!-- 响应信息卡片（箭头上方） -->
<mxCell id="res1_card" value="201 Created&#xa;&#xa;{ &quot;id&quot;: 1, &quot;name&quot;: &quot;Alice&quot; }"
  style="rounded=1;fillColor=#E8F5E9;strokeColor=#43A047;strokeWidth=1;
         fontSize=11;fontColor=#1B5E20;align=left;verticalAlign=top;
         spacingLeft=8;spacingTop=6;spacingRight=8;spacingBottom=6;
         whiteSpace=wrap;fontFamily=Courier New;"
  vertex="1" parent="1">
  <mxGeometry x="CARD_X" y="CARD_Y" width="280" height="CARD_HEIGHT" as="geometry"/>
</mxCell>
```

## HTTP 方法颜色编码

| 方法    | 请求箭头 strokeColor | 卡片 fillColor | 卡片 strokeColor | 语义    |
|--------|---------------------|---------------|-----------------|---------|
| GET    | `#1976D2`           | `#E3F2FD`     | `#1976D2`       | 查询    |
| POST   | `#43A047`           | `#E8F5E9`     | `#43A047`       | 创建    |
| PUT    | `#EF6C00`           | `#FFF3E0`     | `#EF6C00`       | 全量更新 |
| PATCH  | `#FFA000`           | `#FFF8E1`     | `#FFA000`       | 部分更新 |
| DELETE | `#E53935`           | `#FFEBEE`     | `#E53935`       | 删除    |

## 响应状态码颜色

| 状态码范围 | 卡片 fillColor | 卡片 strokeColor | 含义   |
|-----------|---------------|-----------------|--------|
| 2xx       | `#E8F5E9`     | `#43A047`       | 成功   |
| 3xx       | `#E3F2FD`     | `#1976D2`       | 重定向  |
| 4xx       | `#FFF8E1`     | `#FFA000`       | 客户端错误 |
| 5xx       | `#FFEBEE`     | `#E53935`       | 服务端错误 |

## 布局算法

```python
client_center_x = 130
server_center_x = 570
card_width = 280

# 请求卡片在箭头上方，水平居中偏向右侧
req_card_x = (client_center_x + server_center_x) / 2 - card_width / 2
# 响应卡片同理
res_card_x = req_card_x

# 垂直间距
first_call_y = 200                 # 第一个调用起始 y
card_height_base = 70              # 基础卡片高度
card_line_height = 16              # 每行文字高度
call_gap = 30                      # 请求-响应间距
group_gap = 50                     # 调用组间距

current_y = first_call_y

for call in api_calls:
    # 请求卡片
    req_lines = count_lines(call.request_info)
    req_card_h = max(card_height_base, 20 + req_lines * card_line_height)
    draw_request_card(x=req_card_x, y=current_y, height=req_card_h)
    draw_request_arrow(y=current_y + req_card_h + 5)
    current_y += req_card_h + call_gap

    # 响应卡片
    res_lines = count_lines(call.response_info)
    res_card_h = max(card_height_base, 20 + res_lines * card_line_height)
    draw_response_card(x=res_card_x, y=current_y, height=res_card_h)
    draw_response_arrow(y=current_y + res_card_h + 5)
    current_y += res_card_h + group_gap
```

## 请求信息卡片内容格式

```
METHOD /path/to/resource
Header-Name: header-value        (可选，仅展示关键 header)

{                                 (请求体，JSON 格式化)
  "key": "value"
}
```

- 第一行：**HTTP 方法 + 路径**，加粗或用大号字体
- 可选 Header 行：只展示关键的（如 Authorization, Content-Type）
- 请求体：简化的 JSON，省略不重要的字段

## 响应信息卡片内容格式

```
STATUS_CODE Status Text

{                                 (响应体，JSON 格式化)
  "key": "value"
}
```

## 分组标注（可选）

当有多个相关 API 调用时，用分组框标注场景：

```xml
<mxCell id="group1" value="用户注册流程"
  style="rounded=1;fillColor=none;strokeColor=#78909C;strokeWidth=1;dashed=1;
         verticalAlign=top;align=left;spacingTop=5;spacingLeft=10;
         fontSize=12;fontStyle=3;fontColor=#78909C;"
  vertex="1" parent="1">
  <mxGeometry x="40" y="180" width="FULL_WIDTH" height="GROUP_HEIGHT" as="geometry"/>
</mxCell>
```

## 简化模式

当 API 调用超过 5 个时，使用简化模式（省略请求/响应体，只保留方法+路径+状态码）：

```
→  POST /api/users                         201
→  GET  /api/users/1                       200
→  PUT  /api/users/1                       200
→  DELETE /api/users/1                     204
```

## 复杂度控制

- 单张图最多展示 6-8 个 API 调用
- 每个调用的请求/响应体最多 5 行
- 参与者最多 4 个（Client + 2-3 个服务）
- 超过则按场景拆分（如：用户管理 API、订单 API、支付 API）
