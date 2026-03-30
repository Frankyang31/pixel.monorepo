# AI 生成核心 API

> 前端页面：`/studio`（工作台）、`/studio/history`（历史任务）
> 相关 composable：`useGenerationApi.ts`、`useAssetsApi.ts`

---

## 接口列表

| 方法 | 路径 | 说明 | 优先级 |
|------|------|------|--------|
| POST | `/api/v1/generation/jobs` | 提交 AI 生成任务 | P0 |
| GET  | `/api/v1/generation/jobs/:jobId` | 轮询查询任务状态 | P0 |
| GET  | `/api/v1/assets` | 获取用户资产列表（历史任务） | P0 |
| GET  | `/api/v1/assets/:assetId` | 获取单个资产详情 | P1 |
| PATCH | `/api/v1/assets/:assetId` | 更新资产属性（公开/私密/收藏） | P1 |
| DELETE | `/api/v1/assets/:assetId` | 删除资产 | P1 |
| GET  | `/api/v1/assets/:assetId/download` | 获取资产下载链接 | P1 |
| POST | `/api/v1/generation/jobs/:jobId/cancel` | 取消排队中的任务 | P2 |
| GET  | `/api/v1/assets/stats` | 获取用户资产统计信息 | P2 |

---

## 核心概念说明

### 工具 Slug 与积分消耗

| 工具 | toolSlug | 积分/次（约） |
|------|----------|--------------|
| 角色立绘 | `character-art` | 3–5 |
| 场景原画 | `environment-art` | 4–6 |
| Sprite 批量 | `sprite-sheet` | 2/张 |
| 动画预览 | `motion-preview` | 15–25 |
| 材质纹理 | `texture-generator` | 3–5 |
| 细节精修 | `ai-retouch` | 2 |

### 任务状态流转

```
submitted → queued → processing → done
                              ↘ failed
```

| 状态 | 说明 |
|------|------|
| `submitted` | 已提交，等待进队列 |
| `queued` | 已入队，等待 GPU 资源 |
| `processing` | 正在生成中 |
| `done` | 生成完成，资产可访问 |
| `failed` | 生成失败（积分已退还） |

---

## POST /api/v1/generation/jobs

提交一个 AI 生成任务。需要 Bearer Token 和足够的积分。

### 调用页面

`/studio` — 点击「生成」按钮，调用 `useGenerationApi().createJob()`

### Request

```json
{
  "toolSlug": "character-art",
  "prompt": "二次元骑士，银甲红披风，赛璐璐，全身正面立绘，透明背景",
  "negativePrompt": "模糊，低质量，水印，变形",
  "aspectRatio": "2:3",
  "stylePreset": "二次元",
  "referenceImageUrl": null
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| toolSlug | string | ✅ | 工具标识，见上方工具列表 |
| prompt | string | ✅ | 正向提示词，最长 2000 字符 |
| negativePrompt | string | ❌ | 反向提示词，最长 500 字符 |
| aspectRatio | string | ❌ | 画幅比例：`1:1` / `2:3` / `3:4` / `9:16`；默认 `2:3` |
| stylePreset | string | ❌ | 风格预设：`奇幻 RPG` / `二次元` / `像素游戏` / `写实风格` / `赛博朋克` |
| referenceImageUrl | string \| null | ❌ | 参考图 URL（来自 R2 上传，供 img2img 和 inpainting 使用） |

> **当前前端说明**：`negativePrompt` 和 `stylePreset` 已在 UI 中实现，但当前版本未传入接口（代码注释标注了 ⚠️），后端实现后前端会配合开启。

### Response 201

```json
{
  "jobId": "job_abc123def456",
  "status": "queued",
  "estimatedSeconds": 25,
  "creditsCharged": 4,
  "queuePosition": 3
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| jobId | string | 任务唯一 ID，用于后续查询 |
| status | string | 初始状态，通常为 `queued` |
| estimatedSeconds | integer | 预计生成时长（秒），用于前端倒计时 |
| creditsCharged | integer | 本次扣除的积分数 |
| queuePosition | integer | 当前在队列中的位置（1 = 下一个处理） |

### Response 4xx

```json
{ "code": "INSUFFICIENT_CREDITS", "message": "积分不足，当前余额 2 积分，需要 4 积分" }
{ "code": "VALIDATION_ERROR", "message": "prompt 不能为空" }
{ "code": "RATE_LIMITED", "message": "提交过于频繁，请稍后再试" }
```

---

## GET /api/v1/generation/jobs/:jobId

轮询查询单个任务的当前状态。需要 Bearer Token。

### 调用页面

`/studio` — 生成卡片区域的状态轮询（建议轮询间隔 2–3 秒，`status` 变为 `done` 或 `failed` 后停止轮询）

> **轮询策略**：任务提交成功后，前端每隔 2–3 秒调用此接口一次，直到 `status === 'done'` 或 `status === 'failed'` 为止。后端无需维护长连接，实现简单且无超时风险。

### Response 200（生成中）

```json
{
  "jobId": "job_abc123def456",
  "status": "processing",
  "progress": 65,
  "estimatedSecondsRemaining": 9,
  "assetId": null,
  "errorMessage": null
}
```

### Response 200（已完成）

```json
{
  "jobId": "job_abc123def456",
  "status": "done",
  "progress": 100,
  "estimatedSecondsRemaining": 0,
  "assetId": "ast_xyz789",
  "thumbUrl": "https://cdn.pixelmind.app/thumbs/xyz789.webp",
  "downloadUrl": "https://cdn.pixelmind.app/assets/xyz789.png",
  "errorMessage": null
}
```

### Response 200（失败）

```json
{
  "jobId": "job_abc123def456",
  "status": "failed",
  "progress": 0,
  "estimatedSecondsRemaining": 0,
  "assetId": null,
  "errorMessage": "模型服务暂时不可用，积分已退还",
  "creditsRefunded": 4
}
```

#### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| jobId | string | 任务 ID |
| status | string | 当前状态（见状态流转图） |
| progress | integer | 0–100，生成进度百分比 |
| estimatedSecondsRemaining | integer | 预计剩余秒数 |
| assetId | string \| null | 完成后的资产 ID |
| thumbUrl | string \| null | 缩略图 URL（完成后有值） |
| downloadUrl | string \| null | 完整图片/视频下载 URL（完成后有值） |
| errorMessage | string \| null | 失败原因（失败时有值） |
| creditsRefunded | integer \| null | 失败时退还的积分数 |

---

## GET /api/v1/assets

获取当前用户的所有生成资产（历史任务）。需要 Bearer Token。

### 调用页面

`/studio/history` — 历史任务网格（当前使用本地 mock JSON）

### Query Params

| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| page | integer | 1 | 页码 |
| pageSize | integer | 20 | 每页数量，最大 50 |
| toolSlug | string | (全部) | 按工具筛选：`character-art` / `environment-art` / `sprite-sheet` / `motion-preview` / `texture-generator` / `ai-retouch` |
| status | string | (全部) | `done` / `generating` |
| visibility | string | (全部) | `public` / `private` |
| starred | boolean | false | 仅返回收藏的资产 |
| q | string | (无) | 按 prompt 关键词搜索 |
| sort | string | `createdAt_desc` | 排序：`createdAt_desc`（最新）/ `createdAt_asc`（最早）/ `likes_desc`（点赞多） |

### Response 200

```json
{
  "items": [
    {
      "id": "ast_001",
      "jobId": "job_abc001",
      "toolSlug": "character-art",
      "prompt": "二次元骑士，银甲红披风，赛璐璐，全身正面立绘",
      "status": "done",
      "isPublic": false,
      "isStarred": false,
      "thumbUrl": "https://cdn.pixelmind.app/thumbs/001.webp",
      "downloadUrl": "https://cdn.pixelmind.app/assets/001.png",
      "aspectRatio": "2:3",
      "creditsCharged": 4,
      "likesCount": 0,
      "createdAt": "2026-03-30T14:23:00Z",
      "params": "2:3 · 赛璐璐"
    },
    {
      "id": "ast_002",
      "jobId": "job_abc002",
      "toolSlug": "environment-art",
      "prompt": "赛博朋克城市夜景，霓虹灯雨后倒影",
      "status": "generating",
      "isPublic": false,
      "isStarred": false,
      "thumbUrl": null,
      "downloadUrl": null,
      "aspectRatio": "21:9",
      "creditsCharged": 5,
      "likesCount": 0,
      "createdAt": "2026-03-30T16:45:00Z",
      "params": "21:9 · 30 steps"
    }
  ],
  "total": 127,
  "page": 1,
  "pageSize": 20,
  "totalPages": 7,
  "stats": {
    "total": 127,
    "public": 43,
    "generating": 2,
    "totalLikes": 1240
  }
}
```

#### items 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 资产唯一 ID |
| jobId | string | 关联的生成任务 ID |
| toolSlug | string | 生成工具 slug |
| prompt | string | 使用的提示词 |
| status | string | `done` / `generating` / `failed` |
| isPublic | boolean | 是否公开展示 |
| isStarred | boolean | 是否已收藏 |
| thumbUrl | string \| null | 缩略图 URL（生成中时为 null） |
| downloadUrl | string \| null | 下载 URL（生成中时为 null） |
| aspectRatio | string | 画幅比例 |
| creditsCharged | integer | 消耗积分数 |
| likesCount | integer | 获得的点赞数（作品公开后才有意义） |
| createdAt | string | 创建时间 |
| params | string | 参数摘要，面向用户展示（如 `"2:3 · 赛璐璐"`） |

#### stats 字段说明（汇总统计，对应历史页顶部统计栏）

| 字段 | 类型 | 说明 |
|------|------|------|
| total | integer | 总作品数 |
| public | integer | 公开展示中的数量 |
| generating | integer | 正在生成中的数量 |
| totalLikes | integer | 累计获得点赞总数 |

---

## GET /api/v1/assets/:assetId

获取单个资产的完整详情。需要 Bearer Token（且只能访问自己的资产，或已公开的资产）。

### Response 200

单个 `AssetItem` 对象，结构与 `GET /api/v1/assets` 中的 items 元素完全一致，补充以下额外字段：

```json
{
  "id": "ast_001",
  "...": "...(同上)",
  "fullResUrl": "https://cdn.pixelmind.app/assets/001-4k.png",
  "metadata": {
    "width": 2048,
    "height": 3072,
    "format": "PNG",
    "fileSize": 4194304
  }
}
```

---

## PATCH /api/v1/assets/:assetId

更新资产属性（公开/私密切换、收藏）。需要 Bearer Token。

### 调用页面

`/studio/history` — 可见性切换按钮（UI 已渲染，事件处理待绑定）

### Request

```json
{
  "isPublic": true,
  "isStarred": false
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| isPublic | boolean | ❌ | 设置为 true 时，该作品将出现在公开作品广场 |
| isStarred | boolean | ❌ | 收藏/取消收藏 |

### Response 200

返回更新后的资产对象（结构同上）。

---

## DELETE /api/v1/assets/:assetId

删除资产及关联文件。不可逆操作。需要 Bearer Token。

### Response 200

```json
{ "success": true, "creditsRefunded": 0 }
```

> 删除已完成的资产不退还积分，仅删除文件和记录。

---

## GET /api/v1/assets/:assetId/download

获取资产的临时下载签名 URL（有效期 5 分钟，防盗链）。

### Response 200

```json
{
  "url": "https://cdn.pixelmind.app/assets/001.png?sign=xxxxx&expires=1743350000",
  "filename": "pixelmind-character-art-ast001.png",
  "expiresAt": "2026-03-30T17:08:00Z"
}
```

---

## GET /api/v1/assets/stats

获取当前用户的资产统计汇总（用于历史页顶部统计栏）。需要 Bearer Token。

### Response 200

```json
{
  "total": 127,
  "public": 43,
  "generating": 2,
  "totalLikes": 1240,
  "byTool": {
    "character-art": 45,
    "environment-art": 32,
    "sprite-sheet": 28,
    "motion-preview": 8,
    "texture-generator": 9,
    "ai-retouch": 5
  }
}
```

---

*所属模块：generation | 文档版本：v1.0 — 2026-03-30*
