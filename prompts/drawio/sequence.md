---
name: 时序图
triggers: [时序图, sequence, sequence diagram, 时序, 交互图, interaction]
description: 用于绘制对象间的消息交互和时间顺序
---

# 时序图绘制规则

## 核心概念

时序图由**参与者（Actor/Participant）**和**消息（Message）**组成，按时间从上到下排列。

```
  [客户端]        [API]         [数据库]
     │              │              │
     │── 请求登录 ──→│              │
     │              │── 查询用户 ──→│
     │              │←── 返回结果 ──│
     │←── 返回Token──│              │
     │              │              │
```

## 布局算法

### 参与者（顶部）

```python
participant_width = 140       # 参与者框宽度
participant_height = 50       # 参与者框高度
participant_gap = 200         # 参与者间距（中心到中心）
margin_left = 80
margin_top = 80               # 标题下方

# 参与者 x 坐标
for i, p in enumerate(participants):
    p.x = margin_left + i * participant_gap
    p.y = margin_top
    p.center_x = p.x + participant_width / 2
```

### 生命线（Lifeline）

每个参与者下方画一条虚线直到图底部：

```xml
<mxCell id="lifeline_1"
  style="endArrow=none;strokeWidth=1;strokeColor=#BDBDBD;dashed=1;dashPattern=5 5;"
  edge="1" parent="1">
  <mxGeometry relative="1" as="geometry">
    <mxPoint x="CENTER_X" y="PARTICIPANT_BOTTOM" as="sourcePoint"/>
    <mxPoint x="CENTER_X" y="DIAGRAM_BOTTOM" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

### 消息（水平箭头）

```python
message_v_gap = 60            # 消息行间距
first_message_y = margin_top + participant_height + 40

for i, msg in enumerate(messages):
    msg.y = first_message_y + i * message_v_gap
    msg.source_x = participants[msg.from].center_x
    msg.target_x = participants[msg.to].center_x
```

## 参与者样式

| 角色类型   | style                                                              |
|-----------|--------------------------------------------------------------------|
| 用户/人    | `shape=mxgraph.basic.person;fillColor=#E8F5E9;strokeColor=#43A047;fontSize=13;fontStyle=1;` |
| 服务/系统  | `rounded=1;fillColor=#E3F2FD;strokeColor=#1976D2;fontSize=13;fontStyle=1;` |
| 数据库    | `shape=cylinder3;fillColor=#FFF3E0;strokeColor=#EF6C00;fontSize=13;fontStyle=1;size=10;` |
| 外部系统  | `rounded=1;dashed=1;fillColor=#F5F5F5;strokeColor=#9E9E9E;fontSize=13;fontStyle=1;` |

## 消息箭头样式

| 消息类型    | style 关键属性                                                     |
|-----------|-------------------------------------------------------------------|
| 同步请求   | `endArrow=block;endFill=1;strokeWidth=2;strokeColor=#1976D2;`     |
| 同步响应   | `endArrow=open;dashed=1;strokeWidth=1;strokeColor=#78909C;`       |
| 异步消息   | `endArrow=classic;dashed=0;strokeWidth=2;strokeColor=#7B1FA2;`    |
| 自调用     | 使用弯曲连线，从参与者右侧出发绕回自身                                 |

### 消息标签

消息文字放在箭头上方：

```xml
<mxCell id="msg1" value="POST /api/login"
  style="endArrow=block;endFill=1;strokeWidth=2;strokeColor=#1976D2;
         fontSize=12;fontColor=#1976D2;align=center;verticalAlign=bottom;"
  edge="1" parent="1">
  <mxGeometry relative="1" as="geometry">
    <mxPoint x="SOURCE_CENTER_X" y="MSG_Y" as="sourcePoint"/>
    <mxPoint x="TARGET_CENTER_X" y="MSG_Y" as="targetPoint"/>
  </mxGeometry>
</mxCell>
```

**注意**：从右往左的返回消息，文字同样在上方，`align=center`。

## 激活框（Activation Box）

当参与者正在处理时，在生命线上画一个窄矩形：

```xml
<mxCell id="activation_1" value=""
  style="fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=1;"
  vertex="1" parent="1">
  <mxGeometry x="CENTER_X - 8" y="ACTIVATE_START_Y" width="16" height="DURATION" as="geometry"/>
</mxCell>
```

- 宽度固定 16px，居中在生命线上
- 高度从收到请求开始，到发出响应结束
- 嵌套调用时偏移 8px 避免重叠

## 分组框（Fragment）

用于标注循环、条件、并行等：

```xml
<!-- alt/loop/par 框 -->
<mxCell id="fragment1" value="alt [条件成立]"
  style="rounded=0;fillColor=none;strokeColor=#78909C;strokeWidth=1;dashed=1;
         verticalAlign=top;align=left;spacingTop=5;spacingLeft=8;
         fontSize=11;fontStyle=2;fontColor=#78909C;"
  vertex="1" parent="1">
  <mxGeometry x="X" y="Y" width="W" height="H" as="geometry"/>
</mxCell>
```

- `alt` — 条件分支
- `loop` — 循环
- `par` — 并行执行
- `opt` — 可选

## 复杂度控制

- 参与者最多 6 个，超过则拆分
- 消息最多 15 条，超过则分阶段或拆图
- 嵌套激活框最多 2 层
