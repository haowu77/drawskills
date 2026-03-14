---
name: 流程图
triggers: [流程图, flowchart, flow, 流程, process]
description: 用于绘制业务流程、操作步骤、决策分支
---

# 流程图绘制规则

## 布局方向

**默认：从上到下（Top-Down）**

```
    [开始]
       ↓
    [步骤1]
       ↓
    <判断?>  → [分支A]
       ↓
    [步骤2]
       ↓
    [结束]
```

当流程步骤超过 8 个且无复杂分支时，可用从左到右（Left-Right）。

## 节点形状映射

| 含义       | 形状     | drawio style                                                        |
|-----------|---------|---------------------------------------------------------------------|
| 开始/结束  | 圆角矩形 | `rounded=1;arcSize=50;fillColor=#E8F5E9;strokeColor=#43A047;`       |
| 处理步骤   | 矩形     | `rounded=1;arcSize=10;fillColor=#FFFFFF;strokeColor=#455A64;`       |
| 判断/条件  | 菱形     | `shape=rhombus;fillColor=#FFF8E1;strokeColor=#FFA000;`              |
| 输入/输出  | 平行四边形| `shape=parallelogram;fillColor=#E3F2FD;strokeColor=#1976D2;`        |
| 子流程     | 双边矩形 | `shape=process;fillColor=#F3E5F5;strokeColor=#7B1FA2;`              |
| 数据存储   | 圆柱体   | `shape=cylinder3;fillColor=#FCE4EC;strokeColor=#C62828;size=10;`    |
| 文档      | 文档形状  | `shape=document;fillColor=#E0F2F1;strokeColor=#00897B;`             |

## 布局算法

### Top-Down 流程图

```python
# 伪代码
col_center = total_width / 2
current_y = margin_top + title_height

for step in steps:
    if step.type == "decision":
        # 菱形更大，需要更多空间
        node_width = 160
        node_height = 100
        # 分支向左右展开
        left_branch_x = col_center - branch_offset
        right_branch_x = col_center + branch_offset
    else:
        node_width = calculated_width
        node_height = standard_height

    node_x = col_center - node_width / 2
    node_y = current_y
    current_y += node_height + v_gap
```

### 关键规则

1. **主流程居中** — 所有非分支节点 x 坐标对齐中心线
2. **分支对称** — 条件判断的 Yes/No 分支左右对称展开
3. **分支汇合** — 分支结束后必须汇合回主线，用虚线或合并节点
4. **判断标签** — 菱形的出线必须标注条件（"是/否"、"成功/失败"）

```xml
<!-- 判断节点示例 -->
<mxCell id="d1" value="是否通过？"
  style="shape=rhombus;fillColor=#FFF8E1;strokeColor=#FFA000;strokeWidth=2;fontSize=14;fontStyle=1;whiteSpace=wrap;"
  vertex="1" parent="1">
  <mxGeometry x="320" y="300" width="160" height="100" as="geometry"/>
</mxCell>

<!-- Yes 分支 -->
<mxCell id="e_yes" value="是"
  style="endArrow=classic;strokeWidth=2;strokeColor=#43A047;exitX=1;exitY=0.5;entryX=0;entryY=0.5;fontColor=#43A047;fontSize=13;fontStyle=1;"
  edge="1" source="d1" target="step_yes" parent="1"/>

<!-- No 分支 -->
<mxCell id="e_no" value="否"
  style="endArrow=classic;strokeWidth=2;strokeColor=#E53935;exitX=0;exitY=0.5;entryX=1;entryY=0.5;fontColor=#E53935;fontSize=13;fontStyle=1;"
  edge="1" source="d1" target="step_no" parent="1"/>
```

## 流程步骤编号

超过 5 个步骤时，在节点文字前加序号帮助阅读：

```
① 用户提交表单
② 验证输入数据
③ 调用后端 API
④ 保存到数据库
⑤ 返回结果
```

使用带圈数字 ①②③④⑤⑥⑦⑧⑨⑩ 而不是 1. 2. 3.

## 复杂流程的分组

当流程有 3 个以上阶段时，用浅色背景分区标注阶段：

```
┌─ 阶段一：输入 ──────────────┐
│  [步骤1] → [步骤2]           │
└─────────────────────────────┘
        ↓
┌─ 阶段二：处理 ──────────────┐
│  [步骤3] → <判断> → [步骤4]  │
└─────────────────────────────┘
        ↓
┌─ 阶段三：输出 ──────────────┐
│  [步骤5] → [步骤6]           │
└─────────────────────────────┘
```

## 连线颜色语义

- **正常流程**：`strokeColor=#78909C`（灰蓝色）
- **成功路径**：`strokeColor=#43A047`（绿色）
- **失败/异常**：`strokeColor=#E53935`（红色）
- **可选路径**：`strokeColor=#78909C;dashed=1`（灰蓝虚线）
