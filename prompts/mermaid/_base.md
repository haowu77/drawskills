# Mermaid 基础规则

## 1. 输出格式

生成 `.mmd` 文件，内容为纯 Mermaid 语法文本。

```
---
title: 图表标题
---
图表语法...
```

## 2. 全局指令配置

在图表开头通过 `%%` 注释或 frontmatter 配置主题和方向：

```mermaid
---
title: 我的图表
config:
  theme: base
  themeVariables:
    primaryColor: "#E3F2FD"
    primaryBorderColor: "#1976D2"
    primaryTextColor: "#0D47A1"
---
```

## 3. 通用语法规则

### 节点 ID
- 使用有意义的英文 ID：`userLogin`, `dbQuery`, `apiGateway`
- 避免纯数字 ID（可读性差）

### 中文内容
- 中文文字直接放在节点标签中：`A[用户登录]`
- 长文本用 `<br/>` 换行：`A[第一行<br/>第二行]`

### 节点形状
| 形状     | 语法              | 适用场景       |
|---------|-------------------|--------------|
| 矩形     | `A[文字]`         | 处理步骤      |
| 圆角矩形 | `A(文字)`         | 通用节点      |
| 体育场形 | `A([文字])`       | 开始/结束     |
| 菱形     | `A{文字}`         | 判断/条件     |
| 六边形   | `A{{文字}}`       | 准备/预处理    |
| 平行四边形| `A[/文字/]`      | 输入/输出     |
| 圆柱体   | `A[(文字)]`       | 数据库        |
| 圆形     | `A((文字))`       | 连接点        |

### 连线类型
| 类型       | 语法          | 适用场景     |
|-----------|---------------|------------|
| 实线箭头   | `A --> B`     | 正常流向     |
| 虚线箭头   | `A -.-> B`    | 可选/异步    |
| 粗线箭头   | `A ==> B`     | 强调/主流程   |
| 带标签     | `A -->|标签| B`| 条件说明     |
| 无箭头     | `A --- B`     | 无方向关联    |

## 4. 样式定义

通过 `classDef` 和 `class` 应用样式：

```mermaid
classDef primary fill:#E3F2FD,stroke:#1976D2,stroke-width:2px,color:#0D47A1
classDef success fill:#E8F5E9,stroke:#43A047,stroke-width:2px,color:#1B5E20
classDef warning fill:#FFF8E1,stroke:#FFA000,stroke-width:2px,color:#E65100
classDef danger fill:#FFEBEE,stroke:#E53935,stroke-width:2px,color:#B71C1C

class nodeA,nodeB primary
class nodeC success
```

## 5. 子图（Subgraph）

用于分组和分区：

```mermaid
subgraph 区域名称
    A[节点A]
    B[节点B]
end
```

- 子图支持嵌套
- 子图支持方向设定：`subgraph 名称 [direction TB]`

## 6. 复杂度控制

- 单张图表节点数不超过 20 个
- 子图嵌套不超过 2 层
- 连线交叉尽量减少（通过调整节点声明顺序）
- Mermaid 自动布局，无需手动指定坐标

## 7. 文件输出

生成的 `.mmd` 文件保存到当前工作目录或用户指定路径：

```
output.mmd
```

可选渲染命令（提示用户）：
```bash
# 需安装 @mermaid-js/mermaid-cli
npx mmdc -i output.mmd -o output.svg
npx mmdc -i output.mmd -o output.png
```
