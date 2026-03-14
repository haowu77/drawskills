---
name: 架构图
triggers: [架构图, architecture, arch, 架构, system design, 系统设计]
description: 用于绘制系统架构、分层结构、组件关系
---

# 架构图绘制规则

## 布局模式

架构图使用**分层布局（Layered Layout）**，从上到下或从左到右分层。

### 常见分层（Top-Down）

```
┌─────────────── 用户层 ───────────────┐
│  浏览器    移动端 App    第三方系统     │
└──────────────────────────────────────┘
                   ↓
┌─────────────── 接入层 ───────────────┐
│  API Gateway    负载均衡    CDN       │
└──────────────────────────────────────┘
                   ↓
┌─────────────── 服务层 ───────────────┐
│  服务A    服务B    服务C    服务D      │
└──────────────────────────────────────┘
                   ↓
┌─────────────── 数据层 ───────────────┐
│  MySQL    Redis    ElasticSearch     │
└──────────────────────────────────────┘
```

## 层的绘制规则

每层是一个大矩形容器 + 层标签 + 内部组件：

```xml
<!-- 层容器 -->
<mxCell id="layer1" value=""
  style="rounded=1;fillColor=#F5F5F5;strokeColor=#E0E0E0;strokeWidth=2;verticalAlign=top;"
  vertex="1" parent="1">
  <mxGeometry x="60" y="80" width="LAYER_WIDTH" height="LAYER_HEIGHT" as="geometry"/>
</mxCell>

<!-- 层标签（在容器左侧纵向） -->
<mxCell id="layer1_label" value="接入层"
  style="text;fontSize=14;fontStyle=1;fontColor=#757575;rotation=-90;align=center;fillColor=none;strokeColor=none;"
  vertex="1" parent="1">
  <mxGeometry x="65" y="Y_CENTER" width="80" height="30" as="geometry"/>
</mxCell>

<!-- 层内组件 -->
<mxCell id="comp1" value="API Gateway"
  style="rounded=1;fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=2;fontSize=13;fontStyle=1;"
  vertex="1" parent="1">
  <mxGeometry x="120" y="100" width="140" height="60" as="geometry"/>
</mxCell>
```

## 层尺寸计算

```python
# 伪代码
padding = 30                          # 层内边距
label_width = 40                      # 左侧标签宽度
component_gap = 30                    # 组件间距
layer_gap = 40                        # 层间距

# 每层宽度 = 所有组件宽度 + 间距 + 边距 + 标签
layer_width = label_width + padding * 2 + sum(comp_widths) + (n_comps - 1) * component_gap

# 每层高度 = 组件高度 + 上下边距
layer_height = component_height + padding * 2

# 全局宽度取所有层的最大宽度
total_width = max(all_layer_widths)
# 每层实际宽度统一为 total_width（对齐）
```

## 组件形状映射

| 组件类型     | 形状          | style 关键属性                                    |
|-------------|--------------|--------------------------------------------------|
| 服务/应用    | 圆角矩形      | `rounded=1;fillColor=#E3F2FD;strokeColor=#1976D2`|
| 数据库       | 圆柱体        | `shape=cylinder3;fillColor=#FFF3E0;strokeColor=#EF6C00;size=12`|
| 消息队列     | 平行四边形     | `shape=parallelogram;fillColor=#F3E5F5;strokeColor=#7B1FA2`|
| 缓存        | 六边形        | `shape=hexagon;fillColor=#FFEBEE;strokeColor=#C62828`|
| 外部系统     | 虚线矩形      | `rounded=1;dashed=1;fillColor=#FAFAFA;strokeColor=#9E9E9E`|
| 用户/客户端  | 人形          | `shape=mxgraph.basic.person;fillColor=#E8F5E9;strokeColor=#43A047`|
| 负载均衡     | 三角/菱形      | `shape=rhombus;fillColor=#E0F2F1;strokeColor=#00897B`|
| 文件存储     | 文件夹        | `shape=folder;fillColor=#FFF8E1;strokeColor=#F9A825`|

## 连线规则

### 层间连线（纵向）
```
style="endArrow=classic;strokeWidth=2;strokeColor=#BDBDBD;
       exitX=0.5;exitY=1;entryX=0.5;entryY=0;
       edgeStyle=orthogonalEdgeStyle;"
```

### 同层连线（横向）
```
style="endArrow=classic;strokeWidth=1;strokeColor=#90A4AE;dashed=1;
       exitX=1;exitY=0.5;entryX=0;entryY=0.5;"
```

### 连线标签
- 层间连线标注协议/接口：`HTTP/REST`、`gRPC`、`TCP`
- 同层连线标注交互方式：`Event`、`Pub/Sub`、`RPC`

## 技术标注

在组件下方用小号灰色文字标注关键技术：

```xml
<mxCell id="tech_label" value="Go 1.21 · gRPC"
  style="text;fontSize=10;fontColor=#9E9E9E;align=center;fillColor=none;strokeColor=none;"
  vertex="1" parent="1">
  <mxGeometry x="X" y="COMP_BOTTOM+2" width="COMP_WIDTH" height="18" as="geometry"/>
</mxCell>
```

## 数量与复杂度控制

- 每层最多 5-6 个组件，超过则分两行或拆分子图
- 总层数建议 3-5 层，超过 6 层考虑拆为多张图
- 连线交叉超过 3 处时，重新调整组件位置
