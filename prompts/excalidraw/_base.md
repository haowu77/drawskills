# Excalidraw 基础规则

## 1. 输出格式

生成 `.excalidraw` 文件，内容为 JSON 格式。

### 文件结构

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "drawskills",
  "elements": [],
  "appState": {
    "gridSize": null,
    "viewBackgroundColor": "#ffffff"
  },
  "files": {}
}
```

## 2. 元素类型

### 基本元素

| 类型        | type 值       | 用途          |
|------------|--------------|---------------|
| 矩形       | `rectangle`  | 节点、容器     |
| 椭圆       | `ellipse`    | 开始/结束      |
| 菱形       | `diamond`    | 判断/条件      |
| 文字       | `text`       | 标签、标题     |
| 箭头       | `arrow`      | 连线          |
| 线条       | `line`       | 分割线、装饰    |

### 元素通用属性

```json
{
  "id": "unique-id",
  "type": "rectangle",
  "x": 100,
  "y": 200,
  "width": 200,
  "height": 80,
  "angle": 0,
  "strokeColor": "#1976D2",
  "backgroundColor": "#E3F2FD",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "roundness": { "type": 3 },
  "isDeleted": false,
  "boundElements": [],
  "locked": false
}
```

## 3. 坐标系统

- 原点 (0,0) 在左上角
- x 轴向右增长
- y 轴向下增长
- 所有坐标为绝对坐标（像素）

### 布局参数

| 节点数 | 节点间距 | 行间距 | 节点宽度 | 节点高度 |
|--------|---------|--------|---------|---------|
| 1-7    | 240px   | 160px  | 200px   | 80px    |
| 8-15   | 200px   | 130px  | 170px   | 70px    |
| 16+    | 160px   | 110px  | 140px   | 60px    |

## 4. 文字元素

```json
{
  "type": "text",
  "text": "节点文字",
  "fontSize": 16,
  "fontFamily": 1,
  "textAlign": "center",
  "verticalAlign": "middle",
  "containerId": "parent-element-id"
}
```

### fontFamily 值

| 值 | 字体        |
|----|-----------|
| 1  | Hand-drawn（手绘体，默认） |
| 2  | Normal（常规） |
| 3  | Code（等宽） |

**中文内容建议使用 `fontFamily: 2`**（Normal），手绘体对中文支持有限。

## 5. 箭头/连线

```json
{
  "type": "arrow",
  "x": 300,
  "y": 240,
  "width": 200,
  "height": 0,
  "points": [[0, 0], [200, 0]],
  "startBinding": {
    "elementId": "source-id",
    "focus": 0,
    "gap": 5
  },
  "endBinding": {
    "elementId": "target-id",
    "focus": 0,
    "gap": 5
  },
  "startArrowhead": null,
  "endArrowhead": "arrow"
}
```

- `startBinding` / `endBinding` — 绑定到元素，拖动时自动跟随
- `focus` — 连接点在边上的位置（-1 到 1）
- `gap` — 箭头与元素的间距
- `points` — 折线路径点

## 6. 手绘风格控制

| roughness 值 | 风格     |
|-------------|---------|
| 0           | 无手绘效果 |
| 1           | 轻微手绘（默认） |
| 2           | 明显手绘  |

- 正式场合用 `roughness: 0`
- 头脑风暴/白板用 `roughness: 1` 或 `2`

## 7. 分组

通过 `groupIds` 属性将元素分组：

```json
{
  "id": "elem1",
  "groupIds": ["group-1"],
  ...
}
```

同一 `groupIds` 中的元素会一起选中和移动。

## 8. ID 生成

每个元素的 `id` 必须唯一。使用有意义的命名：

- 节点：`node-用户登录`, `node-api-gateway`
- 文字：`text-用户登录`, `label-步骤1`
- 箭头：`arrow-登录-到-验证`

## 9. boundElements 关联

当文字放在形状内部时，需要双向绑定：

```json
// 形状元素
{
  "id": "rect-1",
  "type": "rectangle",
  "boundElements": [{ "id": "text-1", "type": "text" }]
}

// 文字元素
{
  "id": "text-1",
  "type": "text",
  "containerId": "rect-1"
}
```

## 10. 文件输出

生成的文件保存为 `.excalidraw` 扩展名：

```
output.excalidraw
```

用户可通过以下方式打开：
- https://excalidraw.com（拖入文件）
- VS Code Excalidraw 插件
- Obsidian Excalidraw 插件
