# 支付结算 API

> 前端页面：`/pricing`、`/checkout`、`/checkout/result`

---

## 接口列表

| 方法 | 路径 | 说明 | 优先级 |
|------|------|------|--------|
| POST | `/api/v1/checkout/orders` | 创建支付订单 | P0 |
| GET  | `/api/v1/checkout/orders/:orderId` | 获取订单详情 | P0 |
| GET  | `/api/v1/checkout/orders/:orderId/status` | 轮询订单支付状态 | P0 |
| POST | `/api/v1/checkout/orders/:orderId/cancel` | 取消未支付订单 | P1 |
| POST | `/api/v1/checkout/webhook/wechat` | 微信支付异步通知回调 | P0 |
| POST | `/api/v1/checkout/webhook/stripe` | Stripe 支付异步通知回调 | P0 |
| GET  | `/api/v1/checkout/orders` | 获取订单历史列表 | P2 |

---

## 支付流程说明

### 国内用户（微信支付）

```
1. 前端调用 POST /api/v1/checkout/orders
2. 后端返回微信支付 prepay_id / 二维码 URL
3. 前端展示支付二维码或唤起微信支付 SDK
4. 用户完成支付
5. 微信服务器异步回调 POST /api/v1/checkout/webhook/wechat
6. 后端确认支付，更新订单状态，发放积分/订阅
7. 前端轮询 GET /api/v1/checkout/orders/:orderId/status 确认结果
8. 前端跳转到 /checkout/result 展示支付成功
```

### 海外用户（Stripe）

```
1. 前端调用 POST /api/v1/checkout/orders
2. 后端返回 Stripe clientSecret
3. 前端使用 Stripe.js 完成支付
4. Stripe 服务器异步回调 POST /api/v1/checkout/webhook/stripe
5. 后端确认支付，更新订单状态，发放积分/订阅
6. 前端通过 Stripe 支付完成后携带 orderId 跳转到 /checkout/result
7. 前端调用 GET /api/v1/checkout/orders/:orderId/status 确认结果
```

---

## POST /api/v1/checkout/orders

创建支付订单。**需要登录**。

### 调用页面

`/checkout` — 点击「确认支付」按钮（当前页面为 mock，按钮直接跳转 `/checkout/result`）

> **前端设计说明**：
> - 结算页应读取 URL Query `?plan=pro&billing=monthly`，展示套餐详情
> - 支持输入优惠码（`couponCode`）字段已在设计稿中预留
> - 支付方式由后端根据用户 IP/语言自动选择，或前端提供选项

### Request

```json
{
  "planId": "pro",
  "billing": "monthly",
  "couponCode": "NEWUSER20",
  "paymentMethod": "wechat_pay",
  "successUrl": "https://pixelmind.app/checkout/result",
  "cancelUrl": "https://pixelmind.app/pricing"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| planId | string | ✅ | 套餐 ID：`basic` / `pro` / `max` |
| billing | string | ✅ | 计费周期：`monthly` / `yearly` |
| couponCode | string | ❌ | 优惠码（无效码返回错误，不创建订单） |
| paymentMethod | string | ❌ | 支付方式：`wechat_pay` / `stripe`；不传时后端自动判断 |
| successUrl | string | ❌ | 支付成功后的回跳地址（供 Stripe Checkout 使用） |
| cancelUrl | string | ❌ | 取消支付后的回跳地址（供 Stripe Checkout 使用） |

### Response 201

**微信支付：**

```json
{
  "orderId": "ord_abc001",
  "paymentMethod": "wechat_pay",
  "status": "pending",
  "amount": 19.00,
  "currency": "CNY",
  "planId": "pro",
  "billing": "monthly",
  "discountApplied": 0,
  "wechatPay": {
    "prepayId": "wx20260330...",
    "codeUrl": "weixin://wxpay/bizpayurl?pr=xxx",
    "appId": "wx_app_id",
    "timeStamp": "1743350000",
    "nonceStr": "random_nonce",
    "package": "prepay_id=wx20260330...",
    "signType": "RSA",
    "paySign": "signature"
  },
  "stripe": null,
  "expiresAt": "2026-03-30T17:30:00Z"
}
```

**Stripe：**

```json
{
  "orderId": "ord_abc002",
  "paymentMethod": "stripe",
  "status": "pending",
  "amount": 19.00,
  "currency": "USD",
  "planId": "pro",
  "billing": "monthly",
  "discountApplied": 0,
  "wechatPay": null,
  "stripe": {
    "clientSecret": "pi_3xxx_secret_xxx",
    "publishableKey": "pk_live_xxx",
    "checkoutUrl": null
  },
  "expiresAt": "2026-03-30T17:30:00Z"
}
```

#### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| orderId | string | 订单唯一 ID，前端用于后续查询和跳转 |
| paymentMethod | string | 实际使用的支付方式 |
| status | string | 初始状态固定为 `pending` |
| amount | number | 实付金额（已扣除优惠） |
| currency | string | 货币：`CNY`（人民币）/ `USD`（美元） |
| planId | string | 套餐 ID |
| billing | string | 计费周期 |
| discountApplied | number | 优惠金额 |
| wechatPay | object \| null | 微信支付所需参数（非微信支付时为 null） |
| stripe | object \| null | Stripe 支付所需参数（非 Stripe 支付时为 null） |
| expiresAt | string | 订单过期时间（建议 30 分钟） |

### Response 4xx

```json
{ "code": "COUPON_INVALID", "message": "优惠码无效或已过期" }
{ "code": "PLAN_NOT_FOUND", "message": "套餐不存在" }
{ "code": "SUBSCRIPTION_EXISTS", "message": "您已有同等或更高套餐的订阅" }
```

---

## GET /api/v1/checkout/orders/:orderId

获取订单完整详情。**需要登录**（仅能查询自己的订单）。

### 调用页面

`/checkout` — 展示订单金额/套餐信息（当前为 mock）

### Response 200

```json
{
  "orderId": "ord_abc001",
  "status": "pending",
  "paymentMethod": "wechat_pay",
  "planId": "pro",
  "planName": "Pro",
  "billing": "monthly",
  "amount": 19.00,
  "currency": "CNY",
  "discountApplied": 0,
  "couponCode": null,
  "createdAt": "2026-03-30T17:00:00Z",
  "paidAt": null,
  "expiresAt": "2026-03-30T17:30:00Z"
}
```

---

## GET /api/v1/checkout/orders/:orderId/status

轮询查询订单支付状态。**需要登录**。

### 调用页面

`/checkout/result` — 支付结果页，用于确认支付是否成功（当前无任何数据请求）

> **前端轮询建议**：每 2 秒查询一次，最多查询 30 次（约 1 分钟）；超时后提示用户手动刷新或联系客服。

### Response 200

**待支付：**

```json
{
  "orderId": "ord_abc001",
  "status": "pending",
  "paidAt": null,
  "planId": null,
  "creditsGranted": null
}
```

**已支付：**

```json
{
  "orderId": "ord_abc001",
  "status": "paid",
  "paidAt": "2026-03-30T17:05:23Z",
  "planId": "pro",
  "planName": "Pro",
  "billing": "monthly",
  "creditsGranted": 3000,
  "subscriptionId": "sub_xxx",
  "nextRenewalAt": "2026-04-30T00:00:00Z"
}
```

**支付失败：**

```json
{
  "orderId": "ord_abc001",
  "status": "failed",
  "paidAt": null,
  "failureReason": "支付已取消",
  "planId": null,
  "creditsGranted": null
}
```

#### status 枚举

| status | 说明 |
|--------|------|
| `pending` | 待支付 |
| `paid` | 支付成功 |
| `failed` | 支付失败或取消 |
| `expired` | 订单已过期（超过 30 分钟未支付） |
| `refunded` | 已退款 |

| 字段 | 类型 | 说明 |
|------|------|------|
| creditsGranted | integer \| null | 本次充入的积分数（仅 `paid` 状态有值） |
| subscriptionId | string \| null | 创建的订阅 ID |
| nextRenewalAt | string \| null | 下次续费时间 |

---

## POST /api/v1/checkout/orders/:orderId/cancel

取消未支付的待支付订单。**需要登录**。

### Request

无请求体。

### Response 200

```json
{ "success": true, "message": "订单已取消" }
```

### Response 4xx

```json
{ "code": "ORDER_NOT_FOUND", "message": "订单不存在" }
{ "code": "CANNOT_CANCEL", "message": "订单已支付，无法取消" }
```

---

## POST /api/v1/checkout/webhook/wechat

微信支付异步通知回调。**此接口不由前端调用，由微信服务器主动调用**。

> 后端需按微信支付官方文档实现签名验证、业务处理（更新订单状态、发放积分）、返回 XML 响应。

### 微信回调处理流程

```
1. 验证微信签名（防篡改）
2. 查询内部订单（确认订单存在且状态为 pending）
3. 幂等检查（防止重复处理同一笔支付）
4. 更新订单状态为 paid
5. 触发积分发放（写入 credits 流水）
6. 激活订阅（写入 subscription 记录）
7. 返回微信要求的成功响应
```

---

## POST /api/v1/checkout/webhook/stripe

Stripe Webhook 异步通知。**此接口不由前端调用，由 Stripe 服务器主动调用**。

> 后端需验证 Stripe 签名（`Stripe-Signature` Header），处理 `payment_intent.succeeded` 事件。

---

## GET /api/v1/checkout/orders

获取当前用户的历史订单列表。**需要登录**。

### 调用页面

`/account/settings`（待创建）— 账户设置页中的账单历史（P2，暂不优先）

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| page | integer | 1 | 页码 |
| pageSize | integer | 20 | 每页数量 |

### Response 200

```json
{
  "items": [
    {
      "orderId": "ord_abc001",
      "status": "paid",
      "planId": "pro",
      "planName": "Pro",
      "billing": "monthly",
      "amount": 19.00,
      "currency": "CNY",
      "paidAt": "2026-03-01T10:00:00Z"
    }
  ],
  "total": 5,
  "page": 1,
  "pageSize": 20,
  "totalPages": 1
}
```

---

*所属模块：payment | 文档版本：v1.0 — 2026-03-30*
