---
name: 流程图
triggers: [流程图, flowchart, flow, 流程, process]
description: 用于绘制业务流程、操作步骤、决策分支（手写 SVG）
---

# 流程图绘制规则（Hand-written SVG）

## 布局方向

**默认：从上到下（Top-Down）**

主流程居中，判断分支向左右展开。步骤超过 8 个且无复杂分支时可用从左到右。

## 节点形状映射

| 含义       | 形状     | SVG 元素                                                   |
|-----------|---------|-----------------------------------------------------------|
| 开始/结束  | 圆角矩形 | `<rect rx="25" ry="25" fill="#E8F5E9" stroke="#43A047"/>`  |
| 处理步骤   | 矩形     | `<rect rx="9" ry="9" fill="#FFFFFF" stroke="#455A64"/>`    |
| 判断/条件  | 菱形     | `<path d="M cx cy-ry L cx+rx cy L cx cy+ry L cx-rx cy Z" fill="#FFF8E1" stroke="#FFA000"/>` |
| 数据存储   | 圆柱体   | 双 path 圆柱（见 _base.md）                                 |
| 外部系统   | 虚线矩形 | `<rect rx="9" ry="9" stroke-dasharray="6 6"/>`             |

所有节点都需要 `style="fill: light-dark(...); stroke: light-dark(...);"` 属性。

## 连线规则

### 顺序连线（向下）

每两个相邻步骤之间必须有一条连线：
```xml
<!-- 从节点底部 (cx, y+h) 到下一个节点顶部 (cx, nextY) -->
<path d="M {cx} {y+h} L {cx} {nextY-8}"
  fill="none" stroke="#78909c" stroke-width="2"
  style="stroke: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));"/>
<path d="M {cx} {nextY} L {cx-4} {nextY-8} L {cx} {nextY-6} L {cx+4} {nextY-8} Z"
  fill="#78909c" stroke="#78909c" stroke-width="2"
  style="fill: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));
         stroke: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));"/>
```

### 判断分支连线

从菱形出发，Yes/No 分支分别从右侧/底部出：
```xml
<!-- Yes 分支（向右）：从菱形右顶点到右侧节点 -->
<path d="M {cx+rx} {cy} L {targetX-8} {cy}" .../>  <!-- 绿色 #43A047 -->
<!-- No 分支（向下）：从菱形底部顶点到下方节点 -->
<path d="M {cx} {cy+ry} L {cx} {targetY-8}" .../>   <!-- 红色 #E53935 -->
```

分支连线必须标注条件文字（"是/否"、"成功/失败"）。

### 回路/返回连线

使用虚线 `stroke-dasharray="3 3"` 和 L 形路径绕回上方节点。

## 布局算法

```
col_center = total_width / 2
current_y = margin_top + 50 (标题高度)

for step in steps:
    if step.type == "decision":
        node_width = 160, node_height = 100（菱形需要更多空间）
        右侧分支 x = col_center + 200
    else:
        node_width = 根据文字计算, node_height = 60
    node_x = col_center - node_width / 2
    node_y = current_y
    current_y += node_height + v_gap
```

## 连线颜色语义

- **正常流程**：`#78909C`（灰蓝色）
- **成功路径**：`#43A047`（绿色）
- **失败/异常**：`#E53935`（红色）
- **可选/回路**：`#78909C` + `stroke-dasharray="3 3"`

## 流程步骤编号

超过 5 个步骤时加序号：使用带圈数字 ① ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨ ⑩

## 复杂流程分组

超过 3 个阶段时，用层背景（大圆角矩形 + 侧边标签）分组，参考 _base.md 第 6 节。

## 底部注释

可选，在图表底部添加灰色小字说明：
```xml
<switch>
  <foreignObject ...>
    <div ... style="... font-size: 11px; color: light-dark(rgb(158, 158, 158), rgb(101, 101, 101)); ...">
      注释文字
    </div>
  </foreignObject>
  <text ...>注释文字</text>
</switch>
```
