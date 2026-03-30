# Badge 徽章 / 标签

> 最后更新：2026-03-30

---

## 设计目标

Badge 用于状态标记、分类标签和数量提示。需要在视觉上轻量但不被忽略，与周围内容保持合适的层级关系。

## 变体

| 变体 | 类名 | 用途 | 视觉特征 |
|------|------|------|----------|
| Default | `.badge` | 通用标签 | `--bg-overlay` 背景 |
| Brand | `.badge--brand` | 功能/分类标签 | 品牌渐变背景，白色文字 |
| Success | `.badge--success` | 成功/完成 | 绿色系 |
| Warning | `.badge--warning` | 警告/待处理 | 琥珀色系 |
| Error | `.badge--error` | 错误/失败 | 红色系 |
| Info | `.badge--info` | 信息提示 | 蓝色系 |
| Dot | `.badge--dot` | 状态指示点 | 8px 圆点 |

## 尺寸

| 尺寸 | 类名 | 高度 | 字号 | 内距 |
|------|------|------|------|------|
| SM | `.badge--sm` | 20px | 11px | 4px 8px |
| MD（默认） | `.badge--md` | 24px | 12px | 6px 12px |
| LG | `.badge--lg` | 28px | 13px | 6px 14px |

## 带圆点

```html
<span class="badge badge--dot">
  <span class="badge__dot"></span>
  已完成
</span>
```

## 计数徽章

用于图标或按钮右上角的数字提示：

```html
<div class="badge-count-wrapper" style="position:relative;display:inline-block">
  <svg ...><!-- 图标 --></svg>
  <span class="badge-count">3</span>
</div>
```

| 尺寸 | 高度 | 字号 |
|------|------|------|
| SM | 16px | 10px |
| MD | 20px | 11px |
| LG | 24px | 12px |

## 可访问性

- 状态点 `.badge--dot` 中的颜色不作为唯一信息传达方式
- 需搭配文字说明（如 "已完成" 文字 + 绿色点）
- 计数徽章使用 `aria-label` 说明含义

## 代码示例

```html
<!-- 分类标签 -->
<span class="badge badge--brand">角色立绘</span>
<span class="badge badge--brand">奇幻 RPG</span>

<!-- 状态标签 -->
<span class="badge badge--success"><span class="badge__dot"></span>生成完成</span>
<span class="badge badge--warning"><span class="badge__dot"></span>排队中</span>
<span class="badge badge--error">生成失败</span>

<!-- 计数 -->
<div style="position:relative;display:inline-block">
  <button class="btn btn--ghost btn--icon">
    <svg ...><!-- 通知图标 --></svg>
  </button>
  <span class="badge-count" aria-label="3 条新通知">3</span>
</div>
```
