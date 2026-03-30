# 作品广场 API

> 前端页面：`/gallery`（作品广场列表）、`/gallery/[id]`（作品详情）
> 相关 composable：`useGalleryContent.ts`（当前为本地 JSON，待替换为 API）

---

## 接口列表

| 方法 | 路径 | 说明 | 优先级 |
|------|------|------|--------|
| GET  | `/api/v1/gallery` | 获取公开作品列表（瀑布流） | P1 |
| GET  | `/api/v1/gallery/:id` | 获取单件作品详情 | P1 |
| POST | `/api/v1/gallery/:id/like` | 点赞/取消点赞 | P1 |
| GET  | `/api/v1/gallery/stats` | 获取广场统计数据 | P2 |

> **说明**：作品广场展示的是用户主动设置为「公开」的资产，通过 `PATCH /api/v1/assets/:assetId` 设置 `isPublic: true` 后，进入广场审核/展示队列。

---

## GET /api/v1/gallery

获取作品广场的公开作品列表（瀑布流分页）。**不需要登录**。

### 调用页面

`/gallery` — 瀑布流主体（当前使用本地 `data/gallery/zh-CN.json` mock）

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| page | integer | 1 | 页码 |
| pageSize | integer | 20 | 每页数量，最大 50 |
| type | string | (全部) | 作品类型：`character` / `environment` / `sprite` / `motion` |
| sort | string | `latest` | 排序方式：`latest`（最新）/ `hot`（热门，按点赞数） / `featured`（精选） |
| locale | string | `zh-CN` | 内容语言（影响 toolBadge 等展示文字） |

> **前端说明**：广场页面的「今日热门」和「精选」过滤项，通过 `sort=hot` 和 `sort=featured` 参数实现；类型过滤（角色/场景等）通过 `type` 参数实现。

### Response 200

```json
{
  "items": [
    {
      "id": 1,
      "type": "character",
      "thumbUrl": "https://cdn.pixelmind.app/thumbs/gallery/001.webp",
      "thumbAspectRatio": "2:3",
      "thumbLabel": "东非战士",
      "toolBadge": "角色立绘",
      "prompt": "30岁东非女战士，铜质盔甲融合祖鲁与埃塞俄比亚美学，辫发冠，自信正面立姿，赛璐璐游戏美术风格，透明PNG背景",
      "authorId": "usr_amara",
      "authorName": "Amara_Studio",
      "authorRegion": "埃塞俄比亚 · 亚的斯亚贝巴",
      "authorAvatarUrl": null,
      "authorInitial": "A",
      "likesCount": 312,
      "commentsCount": 28,
      "isLikedByMe": false,
      "isFeatured": true,
      "createdAt": "2026-03-28T10:00:00Z"
    }
  ],
  "total": 240,
  "page": 1,
  "pageSize": 20,
  "totalPages": 12,
  "stats": {
    "totalWorks": 240,
    "culturalCoverage": "40+",
    "femaleNonBinaryRatio": "51%",
    "elderlyRatio": "18%",
    "nonEuropeanMythRatio": "23%"
  }
}
```

#### items 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| id | integer | 作品唯一 ID（公开 gallery 中的 ID，非 assetId） |
| type | string | 作品类型：`character` / `environment` / `sprite` / `motion` |
| thumbUrl | string | 缩略图 URL（CDN 地址） |
| thumbAspectRatio | string | 缩略图比例（用于前端瀑布流高度计算） |
| thumbLabel | string | 作品标题/标签（可理解为作品名称） |
| toolBadge | string | 工具标识文字（面向用户展示） |
| prompt | string | 生成使用的提示词（完整或截断版） |
| authorId | string | 作者用户 ID |
| authorName | string | 作者展示名称 |
| authorRegion | string | 作者所在地区（由用户填写或 OAuth 推断） |
| authorAvatarUrl | string \| null | 作者头像 URL |
| authorInitial | string | 作者姓名首字母（头像加载失败时的 fallback） |
| likesCount | integer | 点赞总数 |
| commentsCount | integer | 评论总数（0 时前端不显示评论按钮） |
| isLikedByMe | boolean | 当前登录用户是否已点赞（未登录时固定为 false） |
| isFeatured | boolean | 是否为精选作品 |
| createdAt | string | 发布时间 |

#### stats 字段说明（对应广场页顶部统计栏）

与现有本地 JSON 中的 `stats` 字段对应，初期可为静态配置值，后续接入真实统计。

---

## GET /api/v1/gallery/:id

获取单件公开作品的完整详情。**不需要登录**。

### 调用页面

`/gallery/[id]` — 作品详情页（当前通过本地 JSON 按 ID 查找）

### Path Params

| 参数 | 类型 | 说明 |
|------|------|------|
| id | integer | 作品 ID |

### Response 200

单个作品对象，结构与列表接口中的 items 元素完全一致，追加以下字段：

```json
{
  "id": 1,
  "...": "...(同列表字段)",
  "toolSlug": "character-art",
  "aspectRatio": "2:3",
  "resolution": "2048x3072",
  "stylePreset": "赛璐璐",
  "viewsCount": 1520,
  "relatedWorks": [
    {
      "id": 4,
      "thumbUrl": "https://cdn.pixelmind.app/thumbs/gallery/004.webp",
      "thumbLabel": "朝鲜族传统",
      "type": "character"
    }
  ]
}
```

| 新增字段 | 类型 | 说明 |
|---------|------|------|
| toolSlug | string | 生成工具 slug |
| aspectRatio | string | 画幅比例 |
| resolution | string | 输出分辨率 |
| stylePreset | string \| null | 风格预设 |
| viewsCount | integer | 浏览次数 |
| relatedWorks | array | 相关作品推荐（同类型或同作者，最多 4 件） |

### Response 404

```json
{ "code": "NOT_FOUND", "message": "作品不存在或已被取消公开" }
```

---

## POST /api/v1/gallery/:id/like

对公开作品进行点赞或取消点赞（Toggle）。**需要登录**。

### 调用页面

`/gallery` — 作品卡片上的「❤️ 点赞」按钮（UI 已渲染，事件绑定待实现）

### Path Params

| 参数 | 类型 | 说明 |
|------|------|------|
| id | integer | 作品 ID |

### Request

无请求体。

```
Authorization: Bearer <accessToken>
```

### Response 200

```json
{
  "liked": true,
  "likesCount": 313
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| liked | boolean | 操作后的点赞状态（true=已点赞，false=已取消） |
| likesCount | integer | 操作后的最新点赞总数 |

### Response 401

```json
{ "code": "UNAUTHORIZED", "message": "请先登录后再点赞" }
```

---

## GET /api/v1/gallery/stats

获取广场统计数据（用于广场页头部展示）。**不需要登录**。

### Response 200

```json
{
  "totalWorks": 240,
  "culturalCoverage": "40+",
  "femaleNonBinaryRatio": "51%",
  "elderlyRatio": "18%",
  "nonEuropeanMythRatio": "23%"
}
```

> 初期可为静态配置值，后续接入真实统计计算逻辑。

---

*所属模块：gallery | 文档版本：v1.0 — 2026-03-30*
