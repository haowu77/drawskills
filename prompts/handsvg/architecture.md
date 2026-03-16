---
name: 架构图
triggers: [架构图, architecture, arch, 架构, system design, 系统设计]
description: 用于绘制系统架构、分层结构、组件关系（手写 SVG）
---

# 架构图绘制规则（Hand-written SVG）

## 布局模式

使用**分层布局（Layered Layout）**，从上到下分层。每层是一个大圆角矩形背景 + 左侧旋转标签 + 内部组件节点。

### 常见分层

```
┌─────────── 用户层 ───────────────┐
│  浏览器    移动端    第三方系统     │
└──────────────────────────────────┘
                 ↓
┌─────────── 服务层 ───────────────┐
│  服务A    服务B    服务C          │
└──────────────────────────────────┘
                 ↓
┌─────────── 数据层 ───────────────┐
│  MySQL    Redis    ES            │
└──────────────────────────────────┘
```

## 层的绘制

每层由 3 部分组成：

### 1. 层背景（大圆角矩形）

```xml
<rect x="1" y="{layerY}" width="{totalWidth}" height="{layerHeight}" rx="15" ry="15"
  fill="#fafafa" stroke="#e0e0e0" stroke-width="2"
  style="fill: light-dark(rgb(250, 250, 250), rgb(22, 22, 22));
         stroke: light-dark(rgb(224, 224, 224), rgb(45, 45, 45));"/>
```

### 2. 层标签（左侧旋转 -90 度）

```xml
<g fill="#757575" font-family="Helvetica" font-weight="bold" text-anchor="middle"
   transform="rotate(-90, 23, {layerCenterY})" font-size="13px"
   style="fill: light-dark(rgb(117, 117, 117), rgb(137, 137, 137));">
  <text x="23" y="{layerCenterY + 12}">层名称</text>
</g>
```

### 3. 层内组件节点

从 x=70（标签右侧）开始排列，组件间距 30px：
```xml
<rect x="{compX}" y="{compY}" width="{compW}" height="60" rx="9" ry="9"
  fill="#e3f2fd" stroke="#1976d2" stroke-width="2"
  style="fill: light-dark(rgb(227, 242, 253), rgb(20, 33, 42));
         stroke: light-dark(rgb(25, 118, 210), rgb(79, 159, 238));"/>
<!-- 接上 switch > foreignObject 文本 -->
```

## 层尺寸计算

```
padding = 30              层内边距
label_width = 60          左侧标签占位
component_gap = 30        组件间距
layer_gap = 40            层间距

layer_width = label_width + padding * 2 + sum(comp_widths) + (n_comps - 1) * component_gap
layer_height = comp_height + padding * 2

total_width = max(all layer_widths)    所有层宽度统一取最大值
```

## 组件形状映射

| 组件类型     | SVG 形状        | fill/stroke                           |
|-------------|----------------|---------------------------------------|
| 服务/应用    | `<rect rx="9">` | `#e3f2fd` / `#1976d2`（主要蓝色）      |
| 数据库       | 双 path 圆柱体   | `#fff3e0` / `#ef6c00`（橙色）          |
| 缓存/队列    | `<rect rx="9">` | `#f3e5f5` / `#7b1fa2`（紫色）          |
| 外部系统     | `<rect rx="9" stroke-dasharray="6 6">` | `#fafafa` / `#9e9e9e`（灰色虚线） |
| 配置/工具    | `<rect rx="9">` | `#f5f5f5` / `#bdbdbd`（次要灰色）      |
| 核心/高亮    | `<rect rx="9">` | `#fff8e1` / `#ffa000`（强调黄色）      |
| 成功/输出    | `<rect rx="9">` | `#e8f5e9` / `#43a047`（绿色）          |

所有颜色需转换为 `style="fill: light-dark(...); stroke: light-dark(...);"` 格式。

## 连线规则

### 层间连线（纵向）

从上层组件底部到下层组件顶部。相邻层的组件有调用关系时必须画连线。

**直线连线**（组件垂直对齐时）：
```xml
<path d="M {cx} {srcBottom} L {cx} {dstTop-8}" fill="none" stroke="#78909c" stroke-width="2" .../>
<path d="M {cx} {dstTop} L {cx-4} {dstTop-8} L {cx} {dstTop-6} L {cx+4} {dstTop-8} Z" .../>
```

**L 形连线**（组件不对齐时，使用 Q 曲线圆角）：
```xml
<path d="M {sx} {sy} L {sx} {midY} Q {sx} {midY+10} {sx+dir*10} {midY+10}
         L {ex-dir*10} {midY+10} Q {ex} {midY+10} {ex} {midY+20} L {ex} {ey-6}"
  fill="none" stroke="#78909c" stroke-width="2" .../>
```

### 同层连线（横向）

用虚线表示，颜色稍浅 `#90a4ae`：
```xml
<path d="M {srcRight} {cy} L {dstLeft-8} {cy}"
  fill="none" stroke="#90a4ae" stroke-dasharray="3 3" .../>
```

### 连线标签

标注协议/接口/编号，使用 `<rect>` 白底 + `<text>` ：
- 层间连线标签：协议 `HTTP`、`gRPC`、步骤编号 ① ② ③
- 同层连线标签：`加载配置`、`Event`、`Pub/Sub`

```xml
<rect fill="#ffffff" stroke="none" x="{lx}" y="{ly}" width="{lw}" height="17"
  style="fill: light-dark(#ffffff, var(--ge-dark-color, #121212));"/>
<text x="{lx}" y="{ly+13}" fill="#1976D2" font-family="Helvetica"
  font-weight="bold" font-size="13px"
  style="fill: light-dark(rgb(25, 118, 210), rgb(79, 159, 238));">①</text>
```

## 技术标注

在组件 foreignObject 中用 `<font>` 小号灰色文字标注关键技术：
```html
<b>Pipeline 编排器</b><br/>
<font style="font-size: 10px; color: light-dark(rgb(158, 158, 158), rgb(101, 101, 101));">
  pipeline.py · OCR→检测→遮蔽
</font>
```

## 底部注释

图表最底部可加一行灰色小字摘要：
```xml
<switch>
  <foreignObject ...>
    <div ... style="font-size: 11px; color: light-dark(rgb(158, 158, 158), rgb(101, 101, 101));">
      本地离线运行 · Python 3.10+ · 数据不出设备
    </div>
  </foreignObject>
  <text ...>注释文字</text>
</switch>
```

## 生成步骤

1. 确定层划分和每层包含的组件
2. 计算层尺寸和全局宽度（取最大层宽）
3. 生成所有层背景和标签
4. 生成所有组件节点（含 foreignObject 文本）
5. **逐一检查相邻层间的调用关系，生成连线**（最容易遗漏！）
6. 检查同层横向关系，生成虚线连线
7. 添加标题和底部注释
8. 自查连线数量（如果为 0 则 100% 遗漏了）

## 数量控制

- 每层最多 5-6 个组件，超过则分两行
- 总层数建议 3-5 层
- 连线交叉超过 3 处时重新调整组件位置
