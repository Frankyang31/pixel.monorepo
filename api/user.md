# 用户账户 API

> 前端页面：`/account`、`/account/points`、`/account/membership`
> 相关 composable：`useAuthApi.ts`（`me()`）

---

## 接口列表

| 方法 | 路径 | 说明 | 优先级 |
|------|------|------|--------|
| GET   | `/api/v1/account/credits/balance` | 获取积分余额 | P1 |
| GET   | `/api/v1/account/credits/history` | 获取积分流水记录 | P1 |
| GET   | `/api/v1/account/subscription` | 获取当前会员订阅状态 | P1 |
| PATCH | `/api/v1/account/profile` | 更新用户资料（昵称/头像） | P2 |
| DELETE | `/api/v1/account` | 注销账户 | P2 |

> **说明**：获取用户基础信息（email、credits、planId）请使用 `GET /api/v1/auth/me`，详见 [auth.md](./auth.md)。

---

## GET /api/v1/account/credits/balance

获取当前用户积分余额。需要 Bearer Token。

### 调用页面

`/account/points` — 显示余额卡片（当前为 `—` 占位）

### Request

```
Authorization: Bearer <accessToken>
```

无请求体。

### Response 200

```json
{
  "balance": 1250,
  "unit": "积分",
  "dailyGift": 50,
  "dailyGiftRefreshAt": "2026-03-31T00:00:00Z"
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| balance | integer | 当前可用积分余额 |
| unit | string | 单位标签，显示用，固定为 `"积分"` |
| dailyGift | integer \| null | 免费套餐每日赠送积分数量；付费套餐返回 null |
| dailyGiftRefreshAt | string \| null | 下次刷新赠送积分的时间（ISO 8601 UTC） |

---

## GET /api/v1/account/credits/history

获取积分变动流水记录（充值、消耗、赠送）。需要 Bearer Token。

### 调用页面

`/account/points` — 历史记录列表（当前为静态占位文字）

### Request

```
Authorization: Bearer <accessToken>
```

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| page | integer | 1 | 页码 |
| pageSize | integer | 20 | 每页条数，最大 50 |
| type | string | (全部) | 筛选类型：`consume` / `recharge` / `gift` |

### Response 200

```json
{
  "items": [
    {
      "id": "txn_abc001",
      "type": "consume",
      "amount": -5,
      "description": "角色立绘生成 · jobId: job_xxx",
      "relatedJobId": "job_xxx",
      "createdAt": "2026-03-30T14:23:00Z",
      "balanceAfter": 1245
    },
    {
      "id": "txn_abc002",
      "type": "recharge",
      "amount": 3000,
      "description": "Pro 套餐月度积分",
      "relatedJobId": null,
      "createdAt": "2026-03-01T00:00:00Z",
      "balanceAfter": 3000
    },
    {
      "id": "txn_abc003",
      "type": "gift",
      "amount": 50,
      "description": "每日赠送积分",
      "relatedJobId": null,
      "createdAt": "2026-03-30T00:00:00Z",
      "balanceAfter": 50
    }
  ],
  "total": 42,
  "page": 1,
  "pageSize": 20,
  "totalPages": 3
}
```

#### items 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 流水唯一 ID |
| type | string | `consume`（消耗）/ `recharge`（充值）/ `gift`（赠送） |
| amount | integer | 积分变化量，消耗为负数（如 `-5`），充值/赠送为正数 |
| description | string | 变动描述，面向用户展示 |
| relatedJobId | string \| null | 关联的生成任务 ID（仅消耗类型有值） |
| createdAt | string | 发生时间（ISO 8601 UTC） |
| balanceAfter | integer | 变动后的积分余额 |

---

## GET /api/v1/account/subscription

获取当前用户的会员订阅状态。需要 Bearer Token。

### 调用页面

`/account/membership` — 显示当前套餐名称、状态、续费时间（当前全为占位）

### Request

```
Authorization: Bearer <accessToken>
```

无请求体。

### Response 200

**有效订阅：**

```json
{
  "planId": "pro",
  "planName": "Pro",
  "status": "active",
  "billingCycle": "monthly",
  "currentPeriodStart": "2026-03-01T00:00:00Z",
  "currentPeriodEnd": "2026-04-01T00:00:00Z",
  "renewAt": "2026-04-01T00:00:00Z",
  "cancelAtPeriodEnd": false,
  "creditsThisPeriod": 3000,
  "creditsUsedThisPeriod": 1750
}
```

**免费套餐（无付费订阅）：**

```json
{
  "planId": "free",
  "planName": "免费",
  "status": "active",
  "billingCycle": null,
  "currentPeriodStart": null,
  "currentPeriodEnd": null,
  "renewAt": null,
  "cancelAtPeriodEnd": false,
  "creditsThisPeriod": null,
  "creditsUsedThisPeriod": null
}
```

#### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| planId | string | 套餐 ID：`free` / `basic` / `pro` / `max` |
| planName | string | 套餐展示名称 |
| status | string | `active`（生效中）/ `expired`（已过期）/ `cancelled`（已取消） |
| billingCycle | string \| null | 计费周期：`monthly` / `yearly`；免费套餐为 null |
| currentPeriodStart | string \| null | 当前计费周期开始时间 |
| currentPeriodEnd | string \| null | 当前计费周期结束时间 |
| renewAt | string \| null | 下次续费时间（若 cancelAtPeriodEnd 为 true，则到期不续费） |
| cancelAtPeriodEnd | boolean | 是否已设置「到期不续费」 |
| creditsThisPeriod | integer \| null | 当期总积分配额（按套餐） |
| creditsUsedThisPeriod | integer \| null | 当期已消耗积分 |

---

## PATCH /api/v1/account/profile

更新用户昵称或头像。需要 Bearer Token。

### 调用页面

`/account/settings`（待创建）— 个人设置页

### Request

```json
{
  "displayName": "新昵称",
  "avatarUrl": "https://cdn.pixelmind.app/avatars/new.webp"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| displayName | string | ❌ | 新昵称，最长 50 个字符 |
| avatarUrl | string | ❌ | 新头像 URL（先上传到 R2 再传 URL） |

> 上传头像文件的接口待补充（可在 `/api/v1/upload/avatar` 单独实现）。

### Response 200

返回更新后的用户完整信息（同 `GET /api/v1/auth/me` 格式）。

---

## DELETE /api/v1/account

注销账户，不可逆操作。需要 Bearer Token + 密码二次验证。

### Request

```json
{
  "password": "currentpassword"
}
```

### Response 200

```json
{ "success": true, "message": "账户已注销" }
```

### Response 4xx

```json
{ "code": "INVALID_CREDENTIALS", "message": "密码错误，无法注销账户" }
```

---

*所属模块：user | 文档版本：v1.0 — 2026-03-30*
