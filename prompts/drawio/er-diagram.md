---
name: ER 图
triggers: [ER图, ER diagram, entity relationship, 实体关系, 数据模型, data model, ERD]
description: 用于绘制数据库实体关系、数据模型
---

# ER 图绘制规则

## 布局模式

ER 图使用**网格布局**，实体均匀分布在画布上，关系线连接实体。

```
  [用户] ——1:N——→ [订单] ——1:N——→ [订单项]
                    │
                  N:1
                    ↓
                 [商品] ——N:N——→ [标签]
```

## 实体（Table）绘制

每个实体用表格形式表示，包含表名和字段列表：

```xml
<!-- 实体容器（表头 + 字段） -->
<mxCell id="entity_user" value="用户 (users)"
  style="shape=table;startSize=36;container=1;collapsible=0;
         fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=2;
         fontSize=14;fontStyle=1;fontColor=#0D47A1;
         childLayout=tableLayout;rowLines=1;columnLines=0;"
  vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="220" height="AUTO" as="geometry"/>
</mxCell>
```

**简化方案**（推荐，兼容性更好）：用多行文本模拟表格：

```xml
<mxCell id="entity_user" value="users&#xa;────────────&#xa;🔑 id : BIGINT&#xa;    username : VARCHAR(50)&#xa;    email : VARCHAR(100)&#xa;    created_at : TIMESTAMP"
  style="rounded=0;fillColor=#FFFFFF;strokeColor=#1976D2;strokeWidth=2;
         fontSize=13;fontColor=#263238;align=left;verticalAlign=top;
         spacingLeft=12;spacingTop=8;spacingBottom=8;whiteSpace=wrap;
         overflow=hidden;"
  vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="240" height="160" as="geometry"/>
</mxCell>

<!-- 表头背景 -->
<mxCell id="entity_user_header" value="users"
  style="rounded=0;fillColor=#1976D2;strokeColor=#1976D2;strokeWidth=2;
         fontSize=14;fontStyle=1;fontColor=#FFFFFF;align=center;
         verticalAlign=middle;"
  vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="240" height="36" as="geometry"/>
</mxCell>
```

## 实体尺寸计算

```python
header_height = 36
row_height = 24
padding_bottom = 10
entity_width = 240                   # 固定宽度

entity_height = header_height + len(fields) * row_height + padding_bottom
```

## 布局算法

```python
# 网格布局
cols = min(4, len(entities))         # 每行最多 4 个
rows = ceil(len(entities) / cols)
entity_gap_h = 320                   # 水平间距（留关系线空间）
entity_gap_v = 250                   # 垂直间距

for i, entity in enumerate(entities):
    col = i % cols
    row = i // cols
    entity.x = margin_left + col * entity_gap_h
    entity.y = margin_top + row * entity_gap_v
```

**优化**：把关系密切的实体放相邻位置，减少连线交叉。

## 关系连线

### 基数标注

| 关系   | 源端标注 | 目标端标注 |
|--------|---------|-----------|
| 1:1    | 1       | 1         |
| 1:N    | 1       | *         |
| N:1    | *       | 1         |
| N:N    | *       | *         |

### 连线样式

```xml
<mxCell id="rel1" value=""
  style="endArrow=ERmandOne;startArrow=ERmandOne;strokeWidth=2;strokeColor=#78909C;
         exitX=1;exitY=0.5;entryX=0;entryY=0.5;
         edgeStyle=orthogonalEdgeStyle;curved=0;"
  edge="1" source="entity_user" target="entity_order" parent="1"/>

<!-- 关系标注 -->
<mxCell id="rel1_label" value="下单"
  style="edgeLabel;fontSize=11;fontColor=#616161;fillColor=#FFFFFF;spacingLeft=4;spacingRight=4;"
  vertex="1" connectable="0" parent="rel1">
  <mxGeometry x="0" y="0" relative="1" as="geometry"/>
</mxCell>
```

### ER 箭头类型（drawio 内置）

- `ERmandOne` — 必须一个（|—）
- `ERmandMany` — 必须多个（|<）
- `ERoptionalOne` — 可选一个（○—）
- `ERoptionalMany` — 可选多个（○<）

## 字段标注规范

- 主键：`🔑` 前缀 或 **PK** 标注
- 外键：`🔗` 前缀 或 **FK** 标注
- 非空：字段名后加 `*`
- 类型用大写：`VARCHAR`, `INT`, `TIMESTAMP`

## 配色方案

- 核心实体：`fillColor=#E3F2FD;strokeColor=#1976D2`（蓝色系）
- 关联表/中间表：`fillColor=#F3E5F5;strokeColor=#7B1FA2`（紫色系）
- 配置表/字典表：`fillColor=#FFF8E1;strokeColor=#FFA000`（黄色系）
- 日志/审计表：`fillColor=#F5F5F5;strokeColor=#9E9E9E`（灰色系）
