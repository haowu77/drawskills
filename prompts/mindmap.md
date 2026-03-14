# 思维导图绘制规则

## 布局模式

**中心发散（Radial）**— 中心主题向四周展开。

```
                 [子主题A1]
                /
        [子主题A]
       /         \
      /           [子主题A2]
[中心主题]
      \           [子主题B1]
       \         /
        [子主题B]
                \
                 [子主题B2]
```

## 层级结构

| 层级 | 角色     | 样式特征                            |
|-----|---------|-------------------------------------|
| L0  | 中心主题 | 最大、最深配色、粗边框、大字体         |
| L1  | 一级分支 | 中等大小、主题色、中等字体             |
| L2  | 二级分支 | 较小、浅色、较小字体                  |
| L3  | 三级分支 | 最小、最浅色或白色、小字体             |

## 节点样式

```xml
<!-- L0: 中心主题 -->
<mxCell style="rounded=1;arcSize=50;fillColor=#1976D2;strokeColor=#0D47A1;strokeWidth=3;
               fontSize=20;fontStyle=1;fontColor=#FFFFFF;shadow=1;"/>

<!-- L1: 一级分支 -->
<mxCell style="rounded=1;fillColor=#E3F2FD;strokeColor=#1976D2;strokeWidth=2;
               fontSize=15;fontStyle=1;fontColor=#0D47A1;"/>

<!-- L2: 二级分支 -->
<mxCell style="rounded=1;fillColor=#F5F5F5;strokeColor=#BDBDBD;strokeWidth=1;
               fontSize=13;fontStyle=0;fontColor=#424242;"/>

<!-- L3: 三级分支 -->
<mxCell style="rounded=1;fillColor=#FFFFFF;strokeColor=#E0E0E0;strokeWidth=1;
               fontSize=12;fontStyle=0;fontColor=#757575;"/>
```

## 布局算法

```python
center_x = canvas_width / 2
center_y = canvas_height / 2

# L0: 中心
l0_width, l0_height = 200, 80

# L1: 围绕中心均匀分布
n_branches = len(l1_nodes)
radius_l1 = 250                    # L1 到中心的距离

for i, node in enumerate(l1_nodes):
    # 均匀分配角度，避开正上方（留给标题）
    angle = (2 * PI / n_branches) * i - PI/2 + PI/n_branches
    node.x = center_x + radius_l1 * cos(angle) - node.width/2
    node.y = center_y + radius_l1 * sin(angle) - node.height/2

# L2: 在 L1 基础上继续向外展开
radius_l2 = 180                    # L2 到 L1 的距离

for l1_node in l1_nodes:
    sub_angle_range = PI / 3       # 每个 L1 分支的子节点角度范围
    for j, sub_node in enumerate(l1_node.children):
        sub_angle = l1_node.angle + spread(j, len(children), sub_angle_range)
        sub_node.x = l1_node.center_x + radius_l2 * cos(sub_angle)
        sub_node.y = l1_node.center_y + radius_l2 * sin(sub_angle)
```

## 连线样式

思维导图使用**曲线**连接，不用直角：

```xml
<mxCell style="endArrow=none;strokeWidth=2;strokeColor=#1976D2;
               curved=1;exitX=0.5;exitY=0;entryX=0.5;entryY=1;"
  edge="1" source="center" target="branch1" parent="1"/>
```

- L0→L1：`strokeWidth=3;strokeColor=主题色`
- L1→L2：`strokeWidth=2;strokeColor=较浅色`
- L2→L3：`strokeWidth=1;strokeColor=#BDBDBD`
- 所有连线**无箭头**：`endArrow=none;startArrow=none`

## 节点尺寸

```python
# 根据文字长度动态计算
def calc_size(text, level):
    base_sizes = {0: (200, 80), 1: (150, 55), 2: (130, 45), 3: (110, 35)}
    min_w, h = base_sizes[level]
    font_size = {0: 20, 1: 15, 2: 13, 3: 12}[level]
    char_width = font_size * 0.7  # 英文
    if has_chinese(text):
        char_width = font_size * 1.1
    text_width = len(text) * char_width + 30  # 30px padding
    return max(min_w, min(text_width, 300)), h
```

## 复杂度控制

- L1 分支最多 6-8 个
- 每个 L1 下的 L2 最多 4-5 个
- L3 一般不超过 3 个
- 总节点数控制在 30 以内
- 超过则拆成多张主题图
