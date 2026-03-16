# Hand-written SVG 基础规则

直接生成 draw.io 视觉风格的 SVG XML，无需 MCP 或外部依赖。包含完整 path 连线和箭头渲染。

## 1. SVG 骨架

```xml
<svg xmlns="http://www.w3.org/2000/svg"
     style="background: transparent; background-color: transparent; color-scheme: light dark;"
     xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"
     width="{W}px" height="{H}px" viewBox="0 0 {W} {H}">
  <defs/>
  <g>
    <!-- 所有图形元素放在这里 -->
  </g>
</svg>
```

- viewBox 必须从 `"0 0"` 开始
- W/H 根据内容计算，留 20px 底部/右侧边距

## 2. 自动布局算法

与 drawio 引擎相同的布局参数：

| 节点数 | 节点间距 | 行间距 | 节点最小宽度 | 节点高度 | 字体大小 |
|--------|---------|--------|-------------|---------|---------|
| 1-7    | 220px   | 160px  | 180px       | 70px    | 16px    |
| 8-15   | 180px   | 130px  | 150px       | 60px    | 14px    |
| 16+    | 150px   | 110px  | 130px       | 50px    | 12px    |

**坐标计算公式：**
```
x = margin_left + col_index * (node_width + h_gap)
y = margin_top + row_index * (node_height + v_gap)
margin_left = 60
margin_top = 80（留给标题）
```

**中文内容宽度修正：**
- 纯英文：字符数 x 10px，最小为上表宽度
- 含中文：字符数 x 16px，最小为上表宽度 x 1.3
- 最大宽度不超过 350px

## 3. 节点渲染

### 圆角矩形（服务/组件/步骤）

```xml
<rect x="201" y="75" width="210" height="60" rx="9" ry="9"
  fill="#e3f2fd" stroke="#1976d2" stroke-width="2"
  style="fill: light-dark(rgb(227, 242, 253), rgb(20, 33, 42));
         stroke: light-dark(rgb(25, 118, 210), rgb(79, 159, 238));"/>
```

### 菱形（判断/条件）

用 path 绘制，cx/cy 为中心点，rx/ry 为半径：
```xml
<path d="M {cx} {cy-ry} L {cx+rx} {cy} L {cx} {cy+ry} L {cx-rx} {cy} Z"
  fill="#fff8e1" stroke="#ffa000" stroke-width="2"
  style="fill: light-dark(rgb(255, 248, 225), rgb(30, 24, 4));
         stroke: light-dark(rgb(255, 160, 0), rgb(167, 85, 0));"/>
```

### 圆柱体（数据库/存储）

两个 path：主体 + 顶部椭圆弧：
```xml
<!-- 主体（底部圆弧 + 两侧 + 顶部圆弧） -->
<path d="M {x} {y+12} C {x} {y+5} {x+w} {y+5} {x+w} {y+12}
         L {x+w} {y+h-12} C {x+w} {y+h-5} {x} {y+h-5} {x} {y+h-12} Z"
  fill="#fff3e0" stroke="#ef6c00" stroke-width="2"
  style="fill: light-dark(rgb(255, 243, 224), rgb(36, 26, 9));
         stroke: light-dark(rgb(239, 108, 0), rgb(222, 110, 17));"/>
<!-- 顶部椭圆 -->
<path d="M {x+w} {y+12} C {x+w} {y+19} {x} {y+19} {x} {y+12}"
  fill="none" stroke="#ef6c00" stroke-width="2"
  style="stroke: light-dark(rgb(239, 108, 0), rgb(222, 110, 17));"/>
```

### 虚线边框（外部/可选系统）

在 rect 上添加 `stroke-dasharray="6 6"`：
```xml
<rect ... stroke-dasharray="6 6"
  style="fill: light-dark(rgb(250, 250, 250), rgb(22, 22, 22));
         stroke: light-dark(rgb(158, 158, 158), rgb(101, 101, 101));"/>
```

## 4. 文本渲染

使用 `<switch>` + `<foreignObject>` 实现富文本，带 `<text>` 作为回退：

```xml
<switch>
  <foreignObject style="overflow: visible; text-align: left;"
    pointer-events="none" width="100%" height="100%"
    requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility">
    <div xmlns="http://www.w3.org/1999/xhtml"
      style="display: flex; align-items: unsafe center; justify-content: unsafe center;
             width: {w-2}px; height: 1px; padding-top: {cy}px; margin-left: {x+1}px;">
      <div style="box-sizing: border-box; font-size: 0; text-align: center; color: #000000;">
        <div style="display: inline-block; font-size: 13px; font-family: Helvetica;
                    color: light-dark(#000000, #ffffff); line-height: 1.2;
                    pointer-events: all; font-weight: bold;
                    white-space: normal; word-wrap: normal;">
          标题文字<br/>
          <font style="font-size: 10px; color: light-dark(rgb(158, 158, 158), rgb(101, 101, 101));">
            副标题/描述
          </font>
        </div>
      </div>
    </div>
  </foreignObject>
  <text x="{cx}" y="{cy+4}" fill="light-dark(#000000, #ffffff)"
    font-family="Helvetica" font-size="13px" text-anchor="middle"
    font-weight="bold">回退文字</text>
</switch>
```

关键参数：
- `padding-top` = 节点垂直中心 y 坐标
- `margin-left` = 节点 x + 1
- `width` = 节点 width - 2
- 副标题用 `<font>` 标签配 10px 灰色

### 标题文本（图表顶部）

```xml
<rect x="1" y="0" width="{W-2}" height="40" fill="none" stroke="none"/>
<switch>
  <foreignObject ...>
    <div ... style="... padding-top: 7px; ...">
      <div style="... font-size: 24px; ... color: light-dark(#1a1a2e, #d5d5e6);
                  font-weight: bold; ...">图表标题</div>
    </div>
  </foreignObject>
  <text x="{W/2}" y="31" fill="#1a1a2e" font-size="24px"
    text-anchor="middle" font-weight="bold">图表标题</text>
</switch>
```

## 5. 连线渲染（关键）

每条 edge = **线条 path + 箭头 path**，这是 handsvg 引擎的核心优势。

### 直线连线（垂直）

从 (x1, y1) 到 (x2, y2)，箭头长度 8px：
```xml
<!-- 线条 -->
<path d="M {x1} {y1} L {x1} {y2-8}"
  fill="none" stroke="#78909c" stroke-width="2"
  style="stroke: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));"/>
<!-- 箭头（8px 三角形） -->
<path d="M {x1} {y2} L {x1-4} {y2-8} L {x1} {y2-6} L {x1+4} {y2-8} Z"
  fill="#78909c" stroke="#78909c" stroke-width="2"
  style="fill: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));
         stroke: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));"/>
```

### 直线连线（水平）

从 (x1, y1) 到 (x2, y1)，箭头向右：
```xml
<path d="M {x1} {y1} L {x2-8} {y1}"
  fill="none" stroke="#78909c" stroke-width="2" .../>
<path d="M {x2} {y1} L {x2-8} {y1-4} L {x2-6} {y1} L {x2-8} {y1+4} Z"
  fill="#78909c" stroke="#78909c" stroke-width="2" .../>
```

### L 形正交连线（带圆角）

用 Q（quadratic curve）实现圆角，弯曲半径 10px：
```xml
<!-- 从 (556, 135) 向下拐左到 (326, 208) -->
<path d="M 556 135 L 556 162 Q 556 172 546 172 L 336 172 Q 326 172 326 182 L 326 202"
  fill="none" stroke="#78909c" stroke-width="2"
  style="stroke: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));"/>
<path d="M 326 208 L 322 200 L 326 202 L 330 200 Z"
  fill="#78909c" stroke="#78909c" stroke-width="2"
  style="fill: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));
         stroke: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));"/>
```

**L 形路径公式（向下再向左/右）：**
```
起点 (sx, sy) → 终点 (ex, ey)
中间 Y = (sy + ey) / 2  或自定义拐点
路径：M sx sy  L sx midY  Q sx (midY+10) (sx±10) (midY+10)  L (ex∓10) (midY+10)  Q ex (midY+10) ex (midY+20)  L ex ey-6
```

### 虚线连线

添加 `stroke-dasharray="3 3"` 和减小 stroke-width：
```xml
<path d="M ..." fill="none" stroke="#90a4ae" stroke-dasharray="3 3"
  style="stroke: light-dark(rgb(144, 164, 174), rgb(85, 102, 110));"/>
```

### 连线标签

在连线中点附近放置小文字，先画白色背景矩形再画文字：
```xml
<rect fill="#ffffff" stroke="none" x="{lx}" y="{ly}" width="{lw}" height="14"
  style="fill: light-dark(#ffffff, var(--ge-dark-color, #121212));"/>
<text x="{lx}" y="{ly+11}" fill="#78909C" font-family="Helvetica" font-size="11px"
  style="fill: light-dark(rgb(120, 144, 156), rgb(99, 120, 130));">标签</text>
```

## 6. 分区/层渲染

### 层背景

大圆角矩形：
```xml
<rect x="1" y="55" width="820" height="100" rx="15" ry="15"
  fill="#fafafa" stroke="#e0e0e0" stroke-width="2"
  style="fill: light-dark(rgb(250, 250, 250), rgb(22, 22, 22));
         stroke: light-dark(rgb(224, 224, 224), rgb(45, 45, 45));"/>
```

### 层标签（旋转 -90 度侧边标签）

```xml
<g fill="#757575" font-family="Helvetica" font-weight="bold" text-anchor="middle"
   transform="rotate(-90, 23, {layerCenterY})" font-size="13px"
   style="fill: light-dark(rgb(117, 117, 117), rgb(137, 137, 137));">
  <text x="23" y="{layerCenterY+12}">层名称</text>
</g>
```

`layerCenterY` = 层背景 rect 的 y + height/2

## 7. light-dark() 颜色映射

每个颜色值都需要 light-dark() 对应的深色模式值。常用映射：

| 角色          | light 值    | dark 值      |
|--------------|------------|-------------|
| 主要填充      | #e3f2fd    | rgb(20,33,42)   |
| 主要描边      | #1976d2    | rgb(79,159,238) |
| 背景分区      | #fafafa    | rgb(22,22,22)   |
| 分区描边      | #e0e0e0    | rgb(45,45,45)   |
| 正文黑色      | #000000    | #ffffff         |
| 标题深色      | #1a1a2e    | #d5d5e6         |
| 副标题灰色    | #9e9e9e    | rgb(101,101,101)|
| 连线灰蓝      | #78909c    | rgb(99,120,130) |
| 层标签灰色    | #757575    | rgb(137,137,137)|

规则：深色模式下填充色取互补暗色，描边色适当提亮以保持对比度。

## 8. 配色

使用 `palettes/colors.md` 中的配色方案，与 drawio 引擎共享。将配色表中的 fillColor/strokeColor 转换为 light-dark() 格式。

## 9. 质量检查清单

生成 SVG 后必须验证：
- [ ] viewBox 从 "0 0" 开始，W/H 与实际内容匹配
- [ ] 每条连线都有 line path + arrowhead path 两个元素
- [ ] 所有相邻节点之间都有连线（数连线数量，应接近节点数）
- [ ] 所有 fill/stroke 使用 light-dark() style 属性
- [ ] foreignObject 中的 div 样式正确（padding-top/margin-left 对齐节点中心）
- [ ] XML 合法（所有标签闭合，属性值正确转义）
- [ ] 标题存在且居中
- [ ] 节点对齐（同行 y 一致，同列 x 一致）

## 10. 输出流程

1. 分析需求，规划节点列表和连线关系
2. 计算布局坐标（遵循第 2 节自动布局算法）
3. 按顺序生成 SVG：标题 → 层背景 → 层标签 → 节点 → 连线
4. 用 Write 工具直接写入 `.svg` 文件
5. 按第 9 节清单自查
