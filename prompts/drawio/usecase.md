---
name: 用例图
triggers: [用例图, use case, usecase, 用例, actor]
description: 用于绘制系统用例、参与者与功能的关系
---

# 用例图绘制规则

## 核心元素

| 元素     | 说明                   | 形状         |
|---------|------------------------|-------------|
| Actor   | 参与者（人或外部系统）    | 人形图标     |
| Use Case| 用例（系统功能）         | 椭圆        |
| System  | 系统边界                | 大矩形       |
| 关系    | 关联/包含/扩展/泛化      | 连线        |

## 布局结构

```
[Actor A]                              [Actor B]
    \        ┌── 系统边界 ──────┐        /
     \       │                  │       /
      ——————→│  (用例1)         │←—————
             │                  │
      ——————→│  (用例2)         │←—————
     /       │                  │       \
    /        │  (用例3)         │        \
[Actor C]    └──────────────────┘    [Actor D]
```

**Actor 在左右两侧，Use Case 在中间的系统边界内。**

## 布局算法

```python
# 系统边界
system_x = 300
system_y = margin_top + 60
system_width = 400
system_height = len(usecases) * 100 + 80

# Actor：左侧 Actor 和右侧 Actor 分开
left_actors_x = 80
right_actors_x = system_x + system_width + 120

# Actor 垂直均匀分布
for i, actor in enumerate(left_actors):
    actor.x = left_actors_x
    actor.y = system_y + 60 + i * (system_height / len(left_actors))

# Use Case：在系统边界内垂直排列
usecase_x = system_x + system_width / 2   # 居中
usecase_gap = 90

for i, uc in enumerate(usecases):
    uc.x = usecase_x - uc.width / 2
    uc.y = system_y + 60 + i * usecase_gap
```

## 元素样式

```xml
<!-- Actor（人形） -->
<mxCell id="actor1" value="管理员"
  style="shape=umlActor;verticalLabelPosition=bottom;verticalAlign=top;
         fillColor=#E8F5E9;strokeColor=#43A047;strokeWidth=2;
         fontSize=13;fontStyle=1;fontColor=#2E7D32;"
  vertex="1" parent="1">
  <mxGeometry x="80" y="200" width="40" height="60" as="geometry"/>
</mxCell>

<!-- Use Case（椭圆） -->
<mxCell id="uc1" value="用户登录"
  style="ellipse;fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=2;
         fontSize=14;fontStyle=0;fontColor=#0D47A1;whiteSpace=wrap;"
  vertex="1" parent="1">
  <mxGeometry x="380" y="180" width="160" height="70" as="geometry"/>
</mxCell>

<!-- 系统边界 -->
<mxCell id="system" value="订单管理系统"
  style="rounded=0;fillColor=none;strokeColor=#455A64;strokeWidth=2;
         verticalAlign=top;align=center;spacingTop=10;
         fontSize=16;fontStyle=1;fontColor=#263238;"
  vertex="1" parent="1">
  <mxGeometry x="300" y="120" width="400" height="500" as="geometry"/>
</mxCell>
```

## 关系类型与连线

| 关系类型  | 线型        | 标注                | style                                         |
|----------|------------|--------------------|-------------------------------------------------|
| 关联     | 实线        | 无                 | `endArrow=none;strokeWidth=2;strokeColor=#78909C;` |
| 包含     | 虚线+箭头   | `<<include>>`      | `endArrow=open;dashed=1;strokeColor=#1976D2;`   |
| 扩展     | 虚线+箭头   | `<<extend>>`       | `endArrow=open;dashed=1;strokeColor=#7B1FA2;`   |
| 泛化     | 实线+空心箭头| 无                 | `endArrow=block;endFill=0;strokeColor=#455A64;` |

### 包含/扩展方向

- `<<include>>` 箭头：从**基础用例**指向**被包含用例**
- `<<extend>>` 箭头：从**扩展用例**指向**基础用例**

```xml
<!-- include 关系 -->
<mxCell id="inc1" value="&lt;&lt;include&gt;&gt;"
  style="endArrow=open;dashed=1;strokeWidth=1;strokeColor=#1976D2;
         fontSize=11;fontStyle=2;fontColor=#1976D2;align=center;verticalAlign=bottom;"
  edge="1" source="uc_order" target="uc_auth" parent="1"/>
```

## 复杂度控制

- Actor 最多 4-5 个
- Use Case 最多 8-10 个
- 一张用例图只描述一个子系统或模块
- 避免超过 2 层的 include/extend 嵌套
