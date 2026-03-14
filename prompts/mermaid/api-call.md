---
name: API 调用图
triggers: [API调用, api call, REST, RESTful, 接口调用, api flow, 接口, endpoint, HTTP]
description: 用于展示客户端如何调用 RESTful API 的请求/响应流程（Mermaid 版）
---

# Mermaid API 调用图绘制规则

## 基本结构

API 调用图基于 `sequenceDiagram`，专注展示 HTTP 请求/响应交互：

```mermaid
sequenceDiagram
    autonumber
    actor Client as 客户端
    participant API as API Server
    participant DB as Database

    Client->>+API: POST /api/users<br/>{ "name": "Alice" }
    API->>+DB: INSERT INTO users
    DB-->>-API: OK (id: 1)
    API-->>-Client: 201 Created<br/>{ "id": 1, "name": "Alice" }
```

## 参与者声明

```mermaid
sequenceDiagram
    actor Client as 客户端 (Web/Mobile)
    participant GW as API Gateway
    participant Auth as Auth Service
    participant API as API Server
    participant DB as Database
```

- 客户端用 `actor`（人形图标）
- 服务端用 `participant`（方框）
- 数据库用 `participant` + 名称标识

## HTTP 方法表示

用消息标签展示 HTTP 方法和路径：

```mermaid
sequenceDiagram
    autonumber
    actor C as Client
    participant S as Server

    Note over C,S: 📋 用户 CRUD 接口

    C->>+S: GET /api/users
    S-->>-C: 200 OK [{ id, name }]

    C->>+S: POST /api/users<br/>{ "name": "Alice" }
    S-->>-C: 201 Created { "id": 1 }

    C->>+S: PUT /api/users/1<br/>{ "name": "Bob" }
    S-->>-C: 200 OK { "id": 1, "name": "Bob" }

    C->>+S: DELETE /api/users/1
    S-->>-C: 204 No Content
```

## 请求/响应格式

### 请求消息格式

```
METHOD /path<br/>{ 关键参数 }
```

- 第一行：HTTP 方法 + 路径
- 第二行（可选）：请求体关键字段，用 `<br/>` 换行
- 不要放太多细节，保持简洁

### 响应消息格式

```
STATUS_CODE Status Text<br/>{ 关键返回字段 }
```

- 第一行：状态码 + 状态文字
- 第二行（可选）：响应体关键字段

## 场景分组

用 `rect` 彩色背景 或 `Note` 标注不同场景：

```mermaid
sequenceDiagram
    autonumber
    actor C as Client
    participant S as API Server
    participant DB as Database

    rect rgb(232, 245, 233)
        Note over C,S: 🔐 认证流程
        C->>+S: POST /api/auth/login<br/>{ email, password }
        S->>+DB: SELECT * FROM users
        DB-->>-S: user record
        S-->>-C: 200 OK { token }
    end

    rect rgb(227, 242, 253)
        Note over C,S: 📦 获取数据（带 Token）
        C->>+S: GET /api/users/me<br/>Authorization: Bearer {token}
        S->>+DB: SELECT * FROM users WHERE id=1
        DB-->>-S: user data
        S-->>-C: 200 OK { id, name, email }
    end
```

## 错误处理流程

```mermaid
sequenceDiagram
    actor C as Client
    participant S as API Server

    C->>+S: POST /api/users<br/>{ "email": "invalid" }
    S-->>-C: 400 Bad Request<br/>{ "error": "Invalid email" }

    C->>+S: GET /api/admin/users<br/>Authorization: Bearer {token}
    S-->>-C: 403 Forbidden<br/>{ "error": "Insufficient permissions" }

    C->>+S: GET /api/users/999
    S-->>-C: 404 Not Found
```

## 条件分支（认证检查等）

```mermaid
sequenceDiagram
    actor C as Client
    participant GW as Gateway
    participant Auth as Auth Service
    participant API as API Server

    C->>+GW: GET /api/protected<br/>Authorization: Bearer {token}
    GW->>+Auth: 验证 Token
    alt Token 有效
        Auth-->>-GW: ✅ Valid (user_id: 1)
        GW->>+API: GET /api/protected<br/>X-User-Id: 1
        API-->>-GW: 200 OK { data }
        GW-->>C: 200 OK { data }
    else Token 无效/过期
        Auth-->>GW: ❌ Invalid
        GW-->>-C: 401 Unauthorized
    end
```

## 分页查询

```mermaid
sequenceDiagram
    actor C as Client
    participant S as API Server

    C->>+S: GET /api/users?page=1&size=20
    S-->>-C: 200 OK<br/>{ data: [...], total: 55, page: 1 }

    C->>+S: GET /api/users?page=2&size=20
    S-->>-C: 200 OK<br/>{ data: [...], total: 55, page: 2 }
```

## 样式建议

使用 Note 标注 HTTP 方法的语义颜色：

```mermaid
sequenceDiagram
    actor C as Client
    participant S as Server

    rect rgb(227, 242, 253)
        Note right of C: 🔵 GET - 查询
        C->>+S: GET /api/users
        S-->>-C: 200 OK
    end

    rect rgb(232, 245, 233)
        Note right of C: 🟢 POST - 创建
        C->>+S: POST /api/users
        S-->>-C: 201 Created
    end

    rect rgb(255, 243, 224)
        Note right of C: 🟠 PUT - 更新
        C->>+S: PUT /api/users/1
        S-->>-C: 200 OK
    end

    rect rgb(255, 235, 238)
        Note right of C: 🔴 DELETE - 删除
        C->>+S: DELETE /api/users/1
        S-->>-C: 204 No Content
    end
```

## 复杂度控制

- 单张图最多 6-8 个 API 调用
- 参与者最多 4-5 个
- 请求/响应体只展示关键字段（2-3 个）
- 超过则按场景拆分
- 始终使用 `autonumber` 标注调用顺序
