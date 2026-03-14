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

## ⚠️ 连线是必须的

**每两个相邻步骤之间必须有一条 edge（箭头连线）。** 没有连线的流程图是不完整的。

生成流程图时必须遵循：
1. 先生成所有节点（vertex）
2. **然后为每对相邻节点生成 edge**（箭头连线）
3. 每个 edge 必须有 `source` 和 `target` 属性指向对应节点的 id
4. 开始节点只有出线，结束节点只有入线，其余节点至少一进一出

## 连线 XML 模板

### 普通顺序连线（从上到下）

**每两个相邻步骤之间都必须有这样一条 edge：**

```xml
<mxCell id="edge_2_to_3" value=""
  style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;
         exitX=0.5;exitY=1;entryX=0.5;entryY=0;
         edgeStyle=orthogonalEdgeStyle;curved=1;jettySize=auto;orthogonalLoop=1;"
  edge="1" source="node2" target="node3" parent="1"/>
```

### 判断分支连线（从菱形出发）

```xml
<!-- Yes 分支（向右） -->
<mxCell id="e_yes" value="是"
  style="endArrow=classic;strokeWidth=2;strokeColor=#43A047;
         exitX=1;exitY=0.5;entryX=0;entryY=0.5;
         edgeStyle=orthogonalEdgeStyle;curved=1;
         fontColor=#43A047;fontSize=13;fontStyle=1;"
  edge="1" source="decision1" target="step_yes" parent="1"/>

<!-- No 分支（向下继续主流程） -->
<mxCell id="e_no" value="否"
  style="endArrow=classic;strokeWidth=2;strokeColor=#E53935;
         exitX=0.5;exitY=1;entryX=0.5;entryY=0;
         edgeStyle=orthogonalEdgeStyle;curved=1;
         fontColor=#E53935;fontSize=13;fontStyle=1;"
  edge="1" source="decision1" target="step_no" parent="1"/>
```

## 完整示例（含所有节点和连线）

```xml
<mxGraphModel>
  <root>
    <mxCell id="0"/>
    <mxCell id="1" parent="0"/>

    <!-- 标题 -->
    <mxCell id="title" value="用户登录流程"
      style="text;fontSize=24;fontStyle=1;align=center;fillColor=none;strokeColor=none;fontColor=#1a1a2e;"
      vertex="1" parent="1">
      <mxGeometry x="160" y="20" width="480" height="40" as="geometry"/>
    </mxCell>

    <!-- 节点 -->
    <mxCell id="n1" value="开始"
      style="rounded=1;arcSize=50;fillColor=#E8F5E9;strokeColor=#43A047;strokeWidth=2;fontSize=16;fontStyle=1;whiteSpace=wrap;html=1;"
      vertex="1" parent="1">
      <mxGeometry x="310" y="80" width="180" height="60" as="geometry"/>
    </mxCell>

    <mxCell id="n2" value="① 用户输入账号密码"
      style="rounded=1;arcSize=10;fillColor=#FFFFFF;strokeColor=#455A64;strokeWidth=2;fontSize=14;whiteSpace=wrap;html=1;"
      vertex="1" parent="1">
      <mxGeometry x="280" y="200" width="240" height="60" as="geometry"/>
    </mxCell>

    <mxCell id="n3" value="② 调用登录 API"
      style="rounded=1;arcSize=10;fillColor=#FFFFFF;strokeColor=#455A64;strokeWidth=2;fontSize=14;whiteSpace=wrap;html=1;"
      vertex="1" parent="1">
      <mxGeometry x="280" y="320" width="240" height="60" as="geometry"/>
    </mxCell>

    <mxCell id="n4" value="验证通过？"
      style="shape=rhombus;fillColor=#FFF8E1;strokeColor=#FFA000;strokeWidth=2;fontSize=14;fontStyle=1;whiteSpace=wrap;"
      vertex="1" parent="1">
      <mxGeometry x="320" y="440" width="160" height="100" as="geometry"/>
    </mxCell>

    <mxCell id="n5" value="③ 保存 Token"
      style="rounded=1;arcSize=10;fillColor=#FFFFFF;strokeColor=#455A64;strokeWidth=2;fontSize=14;whiteSpace=wrap;html=1;"
      vertex="1" parent="1">
      <mxGeometry x="280" y="610" width="240" height="60" as="geometry"/>
    </mxCell>

    <mxCell id="n6" value="显示错误提示"
      style="rounded=1;arcSize=10;fillColor=#FFEBEE;strokeColor=#E53935;strokeWidth=2;fontSize=14;whiteSpace=wrap;html=1;"
      vertex="1" parent="1">
      <mxGeometry x="560" y="455" width="200" height="60" as="geometry"/>
    </mxCell>

    <mxCell id="n7" value="结束"
      style="rounded=1;arcSize=50;fillColor=#E8F5E9;strokeColor=#43A047;strokeWidth=2;fontSize=16;fontStyle=1;whiteSpace=wrap;html=1;"
      vertex="1" parent="1">
      <mxGeometry x="310" y="730" width="180" height="60" as="geometry"/>
    </mxCell>

    <!-- ⬇️ 连线（每对相邻节点之间必须有） -->

    <mxCell id="e1" style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;exitX=0.5;exitY=1;entryX=0.5;entryY=0;edgeStyle=orthogonalEdgeStyle;curved=1;"
      edge="1" source="n1" target="n2" parent="1"/>

    <mxCell id="e2" style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;exitX=0.5;exitY=1;entryX=0.5;entryY=0;edgeStyle=orthogonalEdgeStyle;curved=1;"
      edge="1" source="n2" target="n3" parent="1"/>

    <mxCell id="e3" style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;exitX=0.5;exitY=1;entryX=0.5;entryY=0;edgeStyle=orthogonalEdgeStyle;curved=1;"
      edge="1" source="n3" target="n4" parent="1"/>

    <mxCell id="e4" value="是"
      style="endArrow=classic;strokeWidth=2;strokeColor=#43A047;exitX=0.5;exitY=1;entryX=0.5;entryY=0;edgeStyle=orthogonalEdgeStyle;curved=1;fontColor=#43A047;fontSize=13;fontStyle=1;"
      edge="1" source="n4" target="n5" parent="1"/>

    <mxCell id="e5" value="否"
      style="endArrow=classic;strokeWidth=2;strokeColor=#E53935;exitX=1;exitY=0.5;entryX=0;entryY=0.5;edgeStyle=orthogonalEdgeStyle;curved=1;fontColor=#E53935;fontSize=13;fontStyle=1;"
      edge="1" source="n4" target="n6" parent="1"/>

    <mxCell id="e6" style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;dashed=1;exitX=0.5;exitY=1;entryX=1;entryY=0.5;edgeStyle=orthogonalEdgeStyle;curved=1;"
      edge="1" source="n6" target="n2" parent="1"/>

    <mxCell id="e7" style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;exitX=0.5;exitY=1;entryX=0.5;entryY=0;edgeStyle=orthogonalEdgeStyle;curved=1;"
      edge="1" source="n5" target="n7" parent="1"/>

  </root>
</mxGraphModel>
```

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
5. **每对相邻节点必须有 edge 连线** — 这是最重要的规则

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
