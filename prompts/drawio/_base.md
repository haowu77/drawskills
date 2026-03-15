# 基础绘图规则（所有图表类型共享）

## 1. drawio XML 结构

每张图必须包含固定骨架：

```xml
<mxGraphModel>
  <root>
    <mxCell id="0"/>
    <mxCell id="1" parent="0"/>
    <!-- 所有自定义元素从 id="2" 开始 -->
  </root>
</mxGraphModel>
```

- id="0" 和 id="1" 是 drawio 保留节点，不可删除或修改
- 所有顶层图形元素的 parent="1"
- 分组/容器内的子元素 parent 设为容器的 id

## 2. 自动布局算法

根据节点数量选择布局参数：

| 节点数 | 节点间距 | 行间距 | 节点最小宽度 | 节点高度 | 字体大小 |
|--------|---------|--------|-------------|---------|---------|
| 1-7    | 220px   | 160px  | 180px       | 70px    | 16px    |
| 8-15   | 180px   | 130px  | 150px       | 60px    | 14px    |
| 16+    | 150px   | 110px  | 130px       | 50px    | 12px    |

**坐标计算公式：**
```
x = margin_left + col_index × (node_width + h_gap)
y = margin_top + row_index × (node_height + v_gap)
margin_left = 60
margin_top = 80（留给标题）
```

**中文内容宽度修正：**
- 纯英文：按字符数 × 10px，最小为上表宽度
- 含中文：按字符数 × 16px，最小为上表宽度 × 1.3
- 最大宽度不超过 350px（否则换行）

## 3. 通用样式规范

### 节点样式
```
rounded=1;           — 圆角（所有节点默认开启）
whiteSpace=wrap;     — 自动换行
html=1;              — 支持 HTML 标签
shadow=0;            — 默认不加阴影（保持干净）
strokeWidth=2;       — 边框宽度
fontSize=14;         — 默认字体
fontFamily=system-ui; — 系统字体
```

### 连线样式
```
edgeStyle=orthogonalEdgeStyle;  — 正交连线（直角转弯）
curved=1;                       — 曲线平滑
strokeWidth=2;                  — 线条宽度
endArrow=classic;               — 箭头样式
jettySize=auto;                 — 自动转弯距离
orthogonalLoop=1;               — 避免重叠
```

### 连线出入点规则
- **从上到下的流**：exitX=0.5;exitY=1 → entryX=0.5;entryY=0
- **从左到右的流**：exitX=1;exitY=0.5 → entryX=0;entryY=0.5
- **双向连线**：使用相反的两侧，避免重叠
- **分支连线**：从不同的 exitY 出发，确保路径不重叠

### 文字标签在连线上
```
<mxCell style="edgeLabel;..." value="标签文字" .../>
```
不要用 edge 的 value 属性放长文字，应该用单独的 edgeLabel 元素。

## 4. 标题规范

每张图必须有标题：
```xml
<mxCell id="title" value="图表标题"
  style="text;fontSize=24;fontStyle=1;align=center;fillColor=none;strokeColor=none;fontColor=#1a1a2e;"
  vertex="1" parent="1">
  <mxGeometry x="CENTER" y="20" width="FULL_WIDTH" height="40" as="geometry"/>
</mxCell>
```

## 5. 分区/泳道

当需要区分不同区域（如本地 vs 云端、前端 vs 后端）：
```xml
<mxCell id="zone1" value="区域名称"
  style="rounded=1;fillColor=#F5F5F5;strokeColor=#BDBDBD;strokeWidth=2;
         dashed=0;verticalAlign=top;align=left;spacingTop=8;spacingLeft=12;
         fontSize=14;fontStyle=1;fontColor=#616161;"
  vertex="1" parent="1">
  <mxGeometry x="X" y="Y" width="W" height="H" as="geometry"/>
</mxCell>
```

- 分区元素必须放在其内部元素**之前**（先画区域再画节点）
- 分区之间留 30px 间距
- 分区标签在左上角

## 6. 导出注意事项

- **优先使用 SVG** — 自动适配内容大小，不会有大片空白
- **PNG 导出** — 可能包含整个画布空白，适合固定尺寸场景
- **drawio 格式** — 可后续用 draw.io 手动编辑

### ⚠️ SVG viewBox 修正（必做）

draw.io 导出 SVG 时，如果图形不在画布原点附近，导出的 SVG 可能出现：
- `viewBox="0 0 4720 2846"` 但内容在 `x=3899, y=2211` 处
- 渲染结果：大片空白 + 右下角缩小的图

**导出 SVG 后必须检查并修正 viewBox：**
1. 找到 SVG 中所有元素的最小 x/y 和最大 x+width / y+height
2. 将 viewBox 设为 `"minX-20 minY-10 contentWidth+40 contentHeight+20"`（留 padding）
3. 同步更新 `width` 和 `height` 属性为内容实际尺寸

示例修正：
```
<!-- 修正前：大画布，内容在右下角 -->
<svg width="4720px" height="2846px" viewBox="0 0 4720 2846">

<!-- 修正后：裁剪到内容区域 -->
<svg width="860px" height="660px" viewBox="3879 2201 860 660">
```

**根本预防**：遵守第 8 条画布定位规则，所有图形从 x≈60, y≈20 开始绘制，避免导出偏移。

## 7. ⚠️ 连线是必须的

**每张图必须包含 edge（箭头连线）。** 没有连线的图表是不完整的，这是最常见的错误。

关键规则：
- 每条 edge 必须有 `edge="1"` 属性、`source` 和 `target` 属性
- 每条 edge 必须有 `parent="1"`
- edge 的 style 必须包含 `endArrow=classic`（或其他箭头类型）
- 按从上到下的流向，使用 `exitX=0.5;exitY=1;entryX=0.5;entryY=0`
- 生成顺序：先写所有节点（vertex），然后写所有连线（edge）

标准连线模板：
```xml
<mxCell id="edge_A_to_B" style="endArrow=classic;strokeWidth=2;strokeColor=#78909C;exitX=0.5;exitY=1;entryX=0.5;entryY=0;edgeStyle=orthogonalEdgeStyle;curved=1;jettySize=auto;orthogonalLoop=1;"
  edge="1" source="nodeA" target="nodeB" parent="1"/>
```

## 8. 画布定位

**所有图形元素必须从画布左上角附近开始绘制，不要放在画布右下角或远离原点的位置。**

- 第一个元素的 x 坐标从 `60` 开始
- 第一个元素的 y 坐标从 `20`（标题）或 `80`（第一个节点）开始
- 不要使用过大的坐标值（如 x>2000 或 y>2000），除非图表确实很大
- `mxGraphModel` 标签上不要设置 `dx`、`dy` 偏移量，或将其设为 `0`

推荐的 mxGraphModel 属性：
```xml
<mxGraphModel dx="0" dy="0" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="0" pageScale="1" pageWidth="800" pageHeight="600">
```

注意：`page="0"` 表示无限画布模式，避免内容被限制在固定页面区域之外。

## 9. 质量检查清单

画完图后自查：
- [ ] **所有相邻节点之间都有 edge 连线**（最重要！）
- [ ] **图形从画布左上角附近开始**（x≈60, y≈20）
- [ ] 所有节点有清晰的文字标签
- [ ] 连线方向一致（不要忽左忽右忽上忽下）
- [ ] 配色不超过 4-5 种主色
- [ ] 节点对齐（同行的 y 坐标一致，同列的 x 坐标一致）
- [ ] 没有连线穿过节点
- [ ] 字体大小可读（最小 12px）
- [ ] 标题存在且居中
