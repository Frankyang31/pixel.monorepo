# PixelMind API 接口文档

> **产品背景**：游戏资产 AI 内容生成平台，前端为 Vue 3 + Nuxt 3，后端使用 FastAPI（Python）。
> 本目录为前端页面梳理出的接口需求，供后端直接参照开发。

---

## 目录结构

| 文件 | 业务域 |
|------|--------|
| [auth.md](./auth.md) | 认证鉴权（登录 / 注册 / OAuth / 密码重置 / Token 验证） |
| [user.md](./user.md) | 用户账户（个人信息 / 积分余额 / 积分流水 / 会员订阅） |
| [generation.md](./generation.md) | AI 生成核心（提交任务 / 状态轮询 / 资产列表） |
| [gallery.md](./gallery.md) | 作品广场（公开作品列表 / 作品详情 / 点赞） |
| [content.md](./content.md) | 内容类（博客 / 工具目录 / 定价套餐 / Newsletter 订阅） |
| [payment.md](./payment.md) | 支付结算（创建订单 / 支付回调 / 订单状态查询） |

---

## 全局约定

### Base URL

```
Production:  https://api.pixelmind.app
Development: http://localhost:8000
```

前端通过环境变量 `NUXT_PUBLIC_API_BASE` 配置，所有接口路径均以 `/api/v1` 开头。

### 认证方式

需要登录的接口，在请求头中携带 Bearer Token：

```
Authorization: Bearer <accessToken>
```

Token 由 `POST /api/v1/auth/login` 或 `POST /api/v1/auth/register` 返回，前端应存储于 Cookie（HttpOnly）或 localStorage。

> **前端现状**：目前前端使用 `credentials: 'include'`，后端需配置 CORS 允许带凭据，并可选择 Cookie-based Session 替代 Bearer Token。

### 请求/响应格式

- 请求体：`application/json`
- 响应体：`application/json`
- 文件上传：`multipart/form-data`（适用于图片上传场景）

### 分页约定

列表类接口统一使用以下分页参数：

```
Query Params:
  page      integer  default=1    页码（从 1 开始）
  pageSize  integer  default=20   每页数量（最大 100）
```

响应体统一包含：

```json
{
  "items": [],
  "total": 0,
  "page": 1,
  "pageSize": 20,
  "totalPages": 1
}
```

### 时间格式

所有时间字段统一使用 ISO 8601 格式：`2026-03-30T16:53:00Z`（UTC）。

前端展示时按用户本地时区转换。

### 多语言

通过请求头 `Accept-Language` 或查询参数 `locale` 传递语言标识：

```
locale: zh-CN | en
```

> 当前阶段工具目录、博客等内容类接口需支持此参数；认证/支付等业务接口无需区分语言。

---

## 统一错误响应格式

```json
{
  "code": "INVALID_CREDENTIALS",
  "message": "邮箱或密码错误",
  "detail": null
}
```

### 通用错误码

| HTTP Status | code | 说明 |
|-------------|------|------|
| 400 | `VALIDATION_ERROR` | 请求参数校验失败 |
| 401 | `UNAUTHORIZED` | 未登录或 Token 无效/过期 |
| 403 | `FORBIDDEN` | 无权限访问该资源 |
| 404 | `NOT_FOUND` | 资源不存在 |
| 409 | `CONFLICT` | 资源冲突（如邮箱已注册） |
| 422 | `UNPROCESSABLE` | 业务逻辑校验失败 |
| 429 | `RATE_LIMITED` | 请求频率超限 |
| 500 | `INTERNAL_ERROR` | 服务端内部错误 |

### 业务错误码（各模块详见对应文档）

| code | 说明 |
|------|------|
| `EMAIL_EXISTS` | 邮箱已被注册 |
| `INVALID_CREDENTIALS` | 用户名或密码错误 |
| `TOKEN_EXPIRED` | Token 已过期 |
| `INSUFFICIENT_CREDITS` | 积分不足，无法发起生成任务 |
| `JOB_NOT_FOUND` | 生成任务不存在 |
| `ASSET_NOT_FOUND` | 资产文件不存在 |
| `ORDER_NOT_FOUND` | 订单不存在 |
| `PAYMENT_FAILED` | 支付失败 |
| `PLAN_NOT_FOUND` | 套餐不存在 |

---

## 接口优先级说明

### P0 — 核心链路，必须优先实现

1. `POST /api/v1/auth/login` — 登录
2. `POST /api/v1/auth/register` — 注册
3. `GET  /api/v1/auth/me` — 获取当前用户信息
4. `POST /api/v1/generation/jobs` — 提交生成任务
5. `GET  /api/v1/generation/jobs/:jobId` — 查询任务状态
6. `GET  /api/v1/assets` — 获取用户资产列表（历史任务）
7. `POST /api/v1/checkout/orders` — 创建支付订单
8. `GET  /api/v1/checkout/orders/:orderId/status` — 查询订单支付状态

### P1 — 功能完整性，核心链路稳定后实现

1. `POST /api/v1/auth/forgot-password` — 发送密码重置邮件
2. `POST /api/v1/auth/reset-password` — 重置密码
3. `GET  /api/v1/auth/oauth/google` — Google OAuth 跳转
4. `GET  /api/v1/auth/oauth/callback` — OAuth 回调处理
5. `GET  /api/v1/gallery` — 作品广场列表
6. `GET  /api/v1/gallery/:id` — 作品详情
7. `POST /api/v1/gallery/:id/like` — 点赞
8. `GET  /api/v1/account/credits/balance` — 积分余额
9. `GET  /api/v1/account/credits/history` — 积分流水
10. `GET  /api/v1/account/subscription` — 会员订阅状态
11. `GET  /api/v1/blog/posts` — 博客列表

### P2 — 锦上添花，按需迭代

1. `GET  /api/v1/blog/posts/:slug` — 博客详情
2. `GET  /api/v1/tools` — 工具目录（动态化）
3. `GET  /api/v1/tools/:slug` — 工具详情（动态化）
4. `GET  /api/v1/pricing/plans` — 定价套餐（动态化）
5. `POST /api/v1/newsletter/subscribe` — Newsletter 订阅
6. `GET  /api/v1/stats/home` — 首页统计数据

---

## 跨域配置（CORS）

前端开发环境地址为 `http://localhost:3000`，生产环境为 `https://pixelmind.app`。

后端需允许：

```
Access-Control-Allow-Origin: https://pixelmind.app (生产) / http://localhost:3000 (开发)
Access-Control-Allow-Credentials: true
Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
```

---

*文档版本：v1.0 — 2026-03-30*
