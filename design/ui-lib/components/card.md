# Card 卡片

> 最后更新：2026-03-30

---

## 设计目标

Card 是内容承载的基础容器，用于作品展示、数据聚合、功能入口等场景。在深色主题下使用发光边框增强沉浸感，Hover 时有微妙的浮起效果。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default | `.card` | 通用内容卡片 |
| Elevated | `.card--elevated` | 悬浮卡片（更高层级） |
| Interactive | `.card--interactive` | 可点击/悬浮的卡片 |
| Gallery | `.card--gallery` | 作品展示卡片（带图片） |
| Stats | `.card--stats` | 数据统计卡片 |

## 尺寸

| 变体 | 内距 | 圆角 |
|------|------|------|
| Default | 16px | 12px |
| Elevated | 20px | 16px |
| Gallery | 0（图片贴边） | 12px |
| Stats | 20px | 16px |

## 状态

| 状态 | 视觉表现 |
|------|----------|
| Default | 默认样式 |
| Hover | 边框变亮，阴影加深，上移 2px |
| Active | 阴影缩小，轻微下压 |
| Selected | 品牌色边框 `--border-brand` |

## 结构

```html
<div class="card card--gallery card--interactive" role="button" tabindex="0">
  <div class="card__media">
    <img src="..." alt="..." loading="lazy">
  </div>
  <div class="card__body">
    <h3 class="card__title">角色立绘</h3>
    <p class="card__desc">奇幻 RPG 风格精灵游侠</p>
    <div class="card__footer">
      <span class="badge badge--brand">奇幻 RPG</span>
      <span class="card__meta">2 小时前</span>
    </div>
  </div>
</div>
```

## 可访问性

- Interactive 卡片使用 `role="button"` + `tabindex="0"`
- 支持键盘 Enter/Space 激活
- 图片必须有 alt 文本
- 选中状态使用 `aria-pressed="true"`

## CSS 规范

```css
.card {
  background: var(--bg-surface);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-lg);
  overflow: hidden;
  transition:
    border-color var(--transition-fast),
    box-shadow var(--transition-fast),
    transform var(--transition-normal);
}

.card--interactive:hover {
  border-color: var(--border-strong);
  box-shadow: var(--shadow-md);
  transform: translateY(-2px);
}

.card--interactive:focus-visible {
  outline: 2px solid var(--color-brand-mid);
  outline-offset: 2px;
}
```
