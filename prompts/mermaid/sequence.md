---
name: 时序图
triggers: [时序图, sequence, sequence diagram, 时序, 交互图, interaction]
description: 用于绘制对象间的消息交互和时间顺序（Mermaid 版）
---

# Mermaid 时序图绘制规则

## 基本语法

```mermaid
sequenceDiagram
    participant C as 客户端
    participant A as API服务
    participant D as 数据库

    C->>A: POST /api/login
    A->>D: 查询用户
    D-->>A: 返回用户数据
    A-->>C: 返回 Token
```

## 参与者声明

```mermaid
sequenceDiagram
    participant A as 显示名称
    actor U as 用户
```

- `participant` — 方框样式（服务/系统）
- `actor` — 人形图标（用户/操作员）
- `as` 后面是显示名称，支持中文
- 参与者声明顺序决定从左到右排列顺序

## 消息箭头类型

| 类型       | 语法     | 含义          |
|-----------|---------|---------------|
| 同步请求   | `->>` | 实线实心箭头    |
| 同步响应   | `-->>` | 虚线实心箭头    |
| 异步消息   | `-)` | 实线开放箭头    |
| 异步响应   | `--)` | 虚线开放箭头    |

```mermaid
sequenceDiagram
    A->>B: 同步请求
    B-->>A: 同步响应
    A-)B: 异步消息
    B--)A: 异步响应
```

## 激活框

```mermaid
sequenceDiagram
    C->>+A: 请求
    A->>+D: 查询
    D-->>-A: 返回
    A-->>-C: 响应
```

- `+` 激活参与者（开始处理）
- `-` 停用参与者（处理完毕）
- 可嵌套激活

## 组合片段

### 条件分支（alt/else）
```mermaid
sequenceDiagram
    C->>A: 请求
    alt 验证通过
        A-->>C: 200 OK
    else 验证失败
        A-->>C: 401 Unauthorized
    end
```

### 可选（opt）
```mermaid
sequenceDiagram
    C->>A: 请求
    opt 需要缓存
        A->>Cache: 写入缓存
    end
```

### 循环（loop）
```mermaid
sequenceDiagram
    loop 每 5 秒
        C->>A: 心跳检查
        A-->>C: OK
    end
```

### 并行（par）
```mermaid
sequenceDiagram
    par 并行请求
        C->>A: 请求服务A
    and
        C->>B: 请求服务B
    end
```

## 注释

```mermaid
sequenceDiagram
    Note over A,B: 这是跨参与者的注释
    Note right of A: 右侧注释
    Note left of A: 左侧注释
```

## 序号

自动添加消息序号：

```mermaid
sequenceDiagram
    autonumber
    A->>B: 第一条消息
    B->>C: 第二条消息
    C-->>A: 第三条消息
```

## 复杂度控制

- 参与者最多 6 个，超过则拆分
- 消息最多 15 条，超过则分阶段或拆图
- 嵌套组合片段最多 2 层
- 使用 `autonumber` 帮助阅读复杂时序
