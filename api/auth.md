# 认证鉴权 API

> 前端页面：`/login`、`/register`、`/reset-password`、`/auth/callback`（待建）
> 相关 composable：`useAuthApi.ts`

---

## 接口列表

| 方法 | 路径 | 说明 | 优先级 |
|------|------|------|--------|
| POST | `/api/v1/auth/login` | 邮箱密码登录 | P0 |
| POST | `/api/v1/auth/register` | 邮箱注册 | P0 |
| GET  | `/api/v1/auth/me` | 获取当前登录用户信息 | P0 |
| POST | `/api/v1/auth/logout` | 登出（清除会话） | P0 |
| POST | `/api/v1/auth/forgot-password` | 发送密码重置邮件 | P1 |
| POST | `/api/v1/auth/reset-password` | 执行密码重置 | P1 |
| GET  | `/api/v1/auth/oauth/google` | Google OAuth 登录跳转 | P1 |
| GET  | `/api/v1/auth/oauth/wechat` | 微信 OAuth 登录跳转（国内） | P1 |
| GET  | `/api/v1/auth/oauth/callback` | OAuth 回调统一处理 | P1 |
| POST | `/api/v1/auth/refresh` | 刷新 Access Token | P1 |

---

## POST /api/v1/auth/login

用户通过邮箱和密码登录。

### 调用页面

`/login` — `onSubmit()` 函数，调用 `useAuthApi().login()`

### Request

```json
{
  "email": "user@example.com",
  "password": "yourpassword"
}
```

| 字段 | 类型 | 必填 | 校验规则 |
|------|------|------|----------|
| email | string | ✅ | 合法邮箱格式 |
| password | string | ✅ | 最少 6 个字符 |

### Response 200

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": "usr_abc123",
    "email": "user@example.com",
    "displayName": "张三",
    "credits": 150,
    "planId": "free",
    "avatarUrl": null
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| accessToken | string | JWT，前端应存储（Cookie 或 localStorage） |
| tokenType | string | 固定为 `"Bearer"` |
| expiresIn | integer | Token 有效期（秒），建议 3600（1小时） |
| user.id | string | 用户唯一 ID |
| user.email | string | 邮箱 |
| user.displayName | string | 昵称（可为 null，回退显示邮箱前缀） |
| user.credits | integer | 当前积分余额 |
| user.planId | string | 当前套餐 ID：`free` / `basic` / `pro` / `max` |
| user.avatarUrl | string \| null | 头像 URL（可为 null） |

### Response 4xx

```json
{ "code": "INVALID_CREDENTIALS", "message": "邮箱或密码错误" }
{ "code": "VALIDATION_ERROR", "message": "邮箱格式不正确" }
```

---

## POST /api/v1/auth/register

新用户注册。注册成功后自动登录（返回 Token），同时赠送初始积分。

### 调用页面

`/register` — `onSubmit()` 函数，调用 `useAuthApi().register()`

> **注意**：页面支持 `?plan=free|basic|pro` 查询参数（来自定价页跳转），注册完成后应自动引导进入对应套餐的支付流程。此参数目前前端未读取，但后端应记录。

### Request

```json
{
  "email": "user@example.com",
  "password": "yourpassword",
  "displayName": "张三",
  "plan": "free"
}
```

| 字段 | 类型 | 必填 | 校验规则 |
|------|------|------|----------|
| email | string | ✅ | 合法邮箱格式，全局唯一 |
| password | string | ✅ | 最少 8 个字符 |
| displayName | string | ❌ | 最长 50 个字符；不传时系统以邮箱前缀填充 |
| plan | string | ❌ | `free` / `basic` / `pro`，默认 `free` |

> **前端说明**：密码二次确认（`password2`）仅在前端校验，不传给后端。

### Response 201

结构与 `POST /api/v1/auth/login` 的 200 响应完全一致。

注册成功后，根据套餐自动赠送初始积分：

| 套餐 | 初始赠送积分 |
|------|-------------|
| free | 50（每日刷新，非一次性） |
| basic / pro | 注册完成后引导支付，支付后按套餐发放 |

### Response 4xx

```json
{ "code": "EMAIL_EXISTS", "message": "该邮箱已被注册，请直接登录" }
{ "code": "VALIDATION_ERROR", "message": "密码长度不足 8 位" }
```

---

## GET /api/v1/auth/me

获取当前已登录用户的完整信息。需要 Bearer Token。

### 调用页面

- `/account`（账户概览）— 显示邮箱、积分余额
- `/account/points`（积分页）— 显示积分余额
- `/account/membership`（会员页）— 显示当前套餐
- 全局导航栏（头像/积分显示）

### Request

无请求体。需在请求头携带：

```
Authorization: Bearer <accessToken>
```

### Response 200

```json
{
  "id": "usr_abc123",
  "email": "user@example.com",
  "displayName": "张三",
  "avatarUrl": "https://cdn.pixelmind.app/avatars/abc123.webp",
  "credits": 150,
  "planId": "pro",
  "createdAt": "2026-01-15T10:00:00Z"
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 用户唯一 ID |
| email | string | 邮箱 |
| displayName | string | 昵称 |
| avatarUrl | string \| null | 头像 URL |
| credits | integer | 当前积分余额 |
| planId | string | 当前套餐 |
| createdAt | string | 注册时间（ISO 8601） |

### Response 401

```json
{ "code": "UNAUTHORIZED", "message": "Token 无效或已过期" }
```

---

## POST /api/v1/auth/logout

退出登录，使当前 Token 失效（服务端 Token 黑名单或清除 Session Cookie）。

### Request

```
Authorization: Bearer <accessToken>
```

无请求体。

### Response 200

```json
{ "success": true }
```

---

## POST /api/v1/auth/forgot-password

向用户邮箱发送密码重置链接。

### 调用页面

`/reset-password` — Step 1（当前前端将两步合并，后端需拆分流程）

> **当前前端设计**：重置密码页面同时收集邮箱 + 新密码，属于设计简化版。真实流程应为：先填邮箱发送验证邮件 → 用户点击邮件中链接（带 token） → 填写新密码。后端按标准流程实现即可，前端会配合调整。

### Request

```json
{
  "email": "user@example.com"
}
```

### Response 200

```json
{
  "success": true,
  "message": "重置邮件已发送，请查收"
}
```

> 无论邮箱是否存在，均返回 200（防止用户枚举攻击）。

---

## POST /api/v1/auth/reset-password

使用邮件中的重置 Token 设置新密码。

### Request

```json
{
  "token": "reset_token_from_email_link",
  "password": "newpassword123"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| token | string | ✅ | 邮件链接中的一次性 Token（建议有效期 30 分钟） |
| password | string | ✅ | 新密码，最少 8 个字符 |

### Response 200

```json
{ "success": true, "message": "密码已重置，请使用新密码登录" }
```

### Response 4xx

```json
{ "code": "TOKEN_EXPIRED", "message": "重置链接已过期，请重新申请" }
{ "code": "TOKEN_INVALID", "message": "无效的重置链接" }
```

---

## GET /api/v1/auth/oauth/google

发起 Google OAuth 2.0 登录流程，重定向到 Google 授权页。

### 调用页面

`/login` 和 `/register` 页面的「使用 Google 登录」按钮（当前为 mock）。

### Query Params

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| redirect_uri | string | ❌ | 登录成功后的回跳前端页面，默认 `/studio` |

### Response

HTTP 302 重定向到 Google 授权 URL。

---

## GET /api/v1/auth/oauth/wechat

发起微信 OAuth 2.0 登录流程（国内用户），重定向到微信授权页。

参数与 Google OAuth 一致。

---

## GET /api/v1/auth/oauth/callback

OAuth 通用回调处理（Google、微信等均回调此地址）。

> 此接口由后端处理，完成后重定向回前端，并在 URL Query 或 Cookie 中携带 Token。

### Query Params（由 OAuth Provider 提供）

| 参数 | 类型 | 说明 |
|------|------|------|
| code | string | OAuth 授权码 |
| state | string | CSRF 防护随机值 |
| provider | string | `google` / `wechat` |

### 成功行为

后端完成 Token 交换后，携带以下参数重定向回前端：

```
https://pixelmind.app/auth/callback?token=<accessToken>&new_user=true
```

| 参数 | 说明 |
|------|------|
| token | 用户 Access Token |
| new_user | `true` 表示首次注册，前端可展示欢迎弹窗 |

> 前端 `/auth/callback` 页面（待创建）负责解析参数，存储 Token 并跳转到目标页面。

### 失败行为

```
https://pixelmind.app/login?error=oauth_failed
```

---

## POST /api/v1/auth/refresh

刷新 Access Token（使用 Refresh Token）。

> 适用于 Token 过期但用户仍在活跃时的无感续期场景。

### Request

```json
{
  "refreshToken": "rt_xxxxx"
}
```

或通过 Cookie 自动携带（取决于实现方式）。

### Response 200

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 3600
}
```

---

*所属模块：auth | 文档版本：v1.0 — 2026-03-30*
