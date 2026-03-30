# 内容类 API

> 前端页面：`/blog`、`/blog/[slug]`、`/tools`、`/tools/[slug]`、`/pricing`
> 相关 composable：`useBlogApi.ts`、`useToolsApi.ts`（已声明但当前使用本地 JSON）

---

## 接口列表

| 方法 | 路径 | 说明 | 优先级 |
|------|------|------|--------|
| GET  | `/api/v1/blog/posts` | 获取博客文章列表 | P1 |
| GET  | `/api/v1/blog/posts/:slug` | 获取博客文章详情 | P2 |
| POST | `/api/v1/newsletter/subscribe` | Newsletter 邮件订阅 | P2 |
| GET  | `/api/v1/tools` | 获取工具目录列表 | P2 |
| GET  | `/api/v1/tools/:slug` | 获取工具详情 | P2 |
| GET  | `/api/v1/pricing/plans` | 获取定价套餐列表 | P2 |
| GET  | `/api/v1/stats/home` | 获取首页运营统计数据 | P2 |

> **说明**：内容类接口目前前端均使用本地 JSON 数据，已实现了完整的类型声明和 composable 接口。后端实现后，前端仅需切换 composable 中的数据源即可。

---

## GET /api/v1/blog/posts

获取博客文章列表（SSR 渲染，SEO 重要）。**不需要登录**。

### 调用页面

`/blog` — 已通过 `useAsyncData('blog-posts', () => listPosts(...))` 接入，**此接口是前端少数已真实调用的接口之一**。

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| category | string | (全部) | 文章分类：`tutorial`（教程）/ `tips`（技巧）/ `case`（案例） |
| locale | string | `zh-CN` | 内容语言 |
| page | integer | 1 | 页码 |
| pageSize | integer | 20 | 每页数量 |

### Response 200

```json
{
  "items": [
    {
      "slug": "top-10-prompt-tips-for-game-character-art",
      "title": "10 个让角色立绘更精准的 Prompt 技巧",
      "excerpt": "掌握这些关键词组合，减少反复抽卡，让 AI 更理解你的设计意图...",
      "category": "tips",
      "date": "2026-03-20",
      "readMinutes": 8,
      "thumbGrad": "pt1",
      "authorName": "PixelMind",
      "coverUrl": null
    }
  ],
  "total": 24,
  "page": 1,
  "pageSize": 20,
  "totalPages": 2
}
```

#### items 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| slug | string | URL 路由参数，如 `top-10-prompt-tips` |
| title | string | 文章标题 |
| excerpt | string | 摘要，150–200 字，用于列表展示和 SEO |
| category | string | 分类：`tutorial` / `tips` / `case` |
| date | string | 发布日期，格式 `YYYY-MM-DD` |
| readMinutes | integer | 预计阅读时间（分钟） |
| thumbGrad | string | 缩略图渐变标识（`pt1`～`pt6`），前端映射为对应颜色渐变；如有真实封面图则使用 coverUrl |
| authorName | string | 作者名称（当前固定为 `PixelMind`） |
| coverUrl | string \| null | 封面图 URL（可选，有值时前端优先显示图片） |

---

## GET /api/v1/blog/posts/:slug

获取单篇博客文章的完整内容。**不需要登录**。

### 调用页面

`/blog/[slug]` — 已通过 `useAsyncData()` 接入，**此接口是前端已真实调用的接口之一**。

### Path Params

| 参数 | 类型 | 说明 |
|------|------|------|
| slug | string | 文章唯一标识，如 `top-10-prompt-tips` |

### Response 200

`BlogPostDetail = BlogPostSummary & { contentHtml: string }`

```json
{
  "slug": "top-10-prompt-tips-for-game-character-art",
  "title": "10 个让角色立绘更精准的 Prompt 技巧",
  "excerpt": "掌握这些关键词组合...",
  "category": "tips",
  "date": "2026-03-20",
  "readMinutes": 8,
  "thumbGrad": "pt1",
  "authorName": "PixelMind",
  "coverUrl": null,
  "contentHtml": "<h2>1. 明确角色种族与年龄</h2><p>...</p>"
}
```

| 新增字段 | 类型 | 说明 |
|---------|------|------|
| contentHtml | string | 文章完整 HTML 内容，前端通过 `v-html` 渲染 |

### Response 404

```json
{ "code": "NOT_FOUND", "message": "文章不存在" }
```

---

## POST /api/v1/newsletter/subscribe

Newsletter 邮件订阅。**不需要登录**。

### 调用页面

`/blog` — 博客页底部 Newsletter 表单（当前为 mock，`newsletterMock=true` 即展示假提示）

### Request

```json
{
  "email": "user@example.com",
  "locale": "zh-CN"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| email | string | ✅ | 订阅邮箱 |
| locale | string | ❌ | 用户语言偏好，用于邮件内容本地化 |

### Response 200

```json
{
  "success": true,
  "message": "订阅成功！欢迎加入 PixelMind 创作者社群 🎨"
}
```

### Response 409

```json
{ "code": "CONFLICT", "message": "该邮箱已订阅" }
```

---

## GET /api/v1/tools

获取工具目录列表（动态化替换前端本地 JSON）。**不需要登录**。

> **前端现状**：composable `useToolsApi().listCatalog()` 已声明，但工具列表页和工作台当前仍使用本地 `data/tools-page/zh-CN.json`。此接口实现后，前端切换数据源即可。

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| locale | string | `zh-CN` | 内容语言 |

### Response 200

```json
{
  "tools": [
    {
      "slug": "character-art",
      "name": "角色立绘",
      "desc": "生成游戏角色正面/侧面立绘图，支持赛璐璐、厚涂、水墨等多种画风，自动输出透明背景 PNG",
      "category": "character",
      "badge": "hot",
      "cost": "3–5 积分 / 次",
      "tags": ["角色设计", "RPG"],
      "isAvailable": true
    }
  ],
  "categories": [
    { "id": "all", "label": "全部", "count": 6 },
    { "id": "character", "label": "角色与立绘", "count": 1 },
    { "id": "scene", "label": "场景与资产", "count": 2 },
    { "id": "batch", "label": "量产与精修", "count": 2 },
    { "id": "motion", "label": "视频与动效", "count": 1 }
  ]
}
```

---

## GET /api/v1/tools/:slug

获取单个工具的详情页内容（动态化）。**不需要登录**。

> **前端现状**：composable `useToolsApi().getToolDetail(slug)` 已声明。工具详情页当前使用本地 `data/tool-detail/zh-CN.json` + `data/tool-detail-extra/zh-CN.json`。

### Path Params

| 参数 | 类型 | 说明 |
|------|------|------|
| slug | string | 工具标识，如 `character-art` |

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| locale | string | `zh-CN` | 内容语言 |

### Response 200

```json
{
  "slug": "character-art",
  "seo": {
    "title": "AI 角色立绘生成器 — PixelMind",
    "description": "使用 PixelMind 生成 RPG、二游、SLG 角色立绘..."
  },
  "breadcrumbLabel": "角色立绘",
  "heroBadge": "核心工具",
  "heroTitle": "AI 角色立绘",
  "heroTitleGrad": "生成器",
  "heroDesc": "...",
  "heroStats": [
    { "num": "3–5", "label": "积分/张" }
  ],
  "features": [
    { "icon": "🎯", "title": "多画风可控", "body": "...", "tag": "风格" }
  ],
  "howTitle": "三步完成立绘",
  "howSub": "从一句话到可导入素材",
  "howSteps": [
    { "step": "1", "emoji": "📝", "title": "描述角色", "body": "...", "demo": "..." }
  ],
  "gallery": {
    "label": "创作灵感",
    "title": "真实用户作品",
    "sub": "来自全球游戏开发者的立绘案例",
    "filters": [
      { "id": "all", "label": "全部" },
      { "id": "rpg", "label": "奇幻 RPG" }
    ],
    "items": [
      {
        "grad": "gc-east-africa",
        "emoji": "⚔️",
        "prompt": "30岁东非女战士...",
        "params": ["赛璐璐", "2:3", "透明背景"],
        "filterIds": ["rpg"]
      }
    ]
  },
  "prompts": {
    "label": "Prompt 模板",
    "title": "开箱即用的提示词",
    "sub": "复制即可使用",
    "items": [
      {
        "title": "奇幻骑士",
        "badge": "热门",
        "body": "二次元骑士，银甲红披风，赛璐璐，全身正面立绘，透明背景",
        "tags": ["RPG", "骑士", "赛璐璐"]
      }
    ]
  },
  "params": {
    "label": "参数说明",
    "title": "掌握这些参数",
    "titleSub": "生成效果更可控",
    "intro": "...",
    "items": [
      { "icon": "📐", "name": "画幅比例", "desc": "..." }
    ],
    "demo": {
      "windowTitle": "PixelMind Studio",
      "prompt": "二次元骑士，赛璐璐，全身立绘",
      "chips": ["2:3", "赛璐璐", "透明背景"],
      "resultEmojis": ["⚔️", "🛡️", "✨"],
      "btnLabel": "生成立绘"
    }
  },
  "reviews": {
    "label": "用户评价",
    "title": "开发者都在用",
    "rating": "4.9",
    "ratingSub": "基于 820+ 真实评价",
    "items": [
      {
        "avatar": "A",
        "name": "Amara_Studio",
        "role": "独立游戏开发者",
        "stars": 5,
        "content": "非常适合独立开发者，出图速度快..."
      }
    ]
  },
  "relatedSlugs": ["environment-art", "ai-retouch"]
}
```

### Response 404

```json
{ "code": "NOT_FOUND", "message": "工具不存在" }
```

---

## GET /api/v1/pricing/plans

获取定价套餐列表（动态化，支持月/年计费切换）。**不需要登录**。

> **前端现状**：定价页使用本地 `data/pricing/zh-CN.json`，月/年切换目前仅是 UI 状态切换而未实际改变价格数据，后端实现后补全。

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| billing | string | `monthly` | 计费周期：`monthly` / `yearly` |
| locale | string | `zh-CN` | 内容语言 |

### Response 200

```json
{
  "billing": "monthly",
  "yearlyDiscount": "8折",
  "plans": [
    {
      "id": "free",
      "tier": "免费",
      "price": "$0",
      "priceMonthly": 0,
      "priceYearly": 0,
      "period": "永久",
      "desc": "体验核心能力，每日赠送积分",
      "credits": "50 积分/日",
      "isFeatured": false,
      "cta": "ghost",
      "ctaText": "免费开始",
      "features": [
        { "text": "角色立绘 & 场景原画", "ok": true },
        { "text": "最大 1K 分辨率", "ok": true },
        { "text": "4K 与商业授权", "ok": false },
        { "text": "优先队列", "ok": false }
      ]
    }
  ],
  "compare": [
    {
      "name": "月度积分",
      "free": "50/日",
      "basic": "800",
      "pro": "3,000",
      "max": "10,000"
    }
  ]
}
```

---

## GET /api/v1/stats/home

获取首页展示的运营统计数据。**不需要登录**。

> **前端现状**：首页当前完全静态，此接口为可选优化，用于展示真实运营数据（如总生成量、活跃用户数等）。

### Response 200

```json
{
  "totalGenerated": "200K+",
  "totalDevelopers": "10K+",
  "totalTools": 6,
  "avgGenerateSeconds": "~6s"
}
```

---

*所属模块：content | 文档版本：v1.0 — 2026-03-30*
