# Tooltip 提示气泡

> 最后更新：2026-03-30

---

## 设计目标

Tooltip 用于提供上下文相关的辅助信息，用户 hover 或 focus 时显示。需要轻量、定位准确、不遮挡关键内容。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default | `.tooltip` | 悬浮提示 |
| Light | `.tooltip--light` | 浅色气泡（适合深色内容区） |

## 尺寸

| 属性 | 值 |
|------|-----|
| 最大宽度 | 240px |
| 内距 | 6px 10px |
| 字号 | 12px |
| 行高 | 16px |
| 箭头大小 | 6px |
| 偏移距离 | 8px（气泡与目标元素间距） |

## 位置

Top（默认）/ Bottom / Left / Right

## 状态

| 状态 | 视觉表现 |
|------|----------|
| Hidden | `opacity: 0`，`pointer-events: none`，`visibility: hidden` |
| Visible | `opacity: 1`，`visibility: visible`，入场动画 150ms |

## 结构

```html
<div class="tooltip-wrapper">
  <button class="btn btn--ghost btn--icon" aria-describedby="tip1">
    <svg>...</svg>
  </button>
  <div class="tooltip" id="tip1" role="tooltip">
    <span class="tooltip__text">下载高清原图</span>
  </div>
</div>
```

## 可访问性

- 使用 `role="tooltip"`
- 触发元素使用 `aria-describedby` 关联 tooltip
- 仅在 hover/focus 时显示，不使用纯 CSS hover（考虑触屏设备）
- 延迟 400ms 后显示，100ms 后隐藏

## CSS 规范

```css
.tooltip-wrapper { position: relative; display: inline-block; }

.tooltip {
  position: absolute;
  bottom: calc(100% + 8px);
  left: 50%;
  transform: translateX(-50%);
  background: var(--bg-elevated);
  color: var(--text-primary);
  font-size: var(--text-xs);
  padding: 6px 10px;
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-md);
  white-space: nowrap;
  z-index: var(--z-tooltip);
  opacity: 0; visibility: hidden;
  transition: opacity 150ms var(--ease-default), visibility 150ms;
}

.tooltip::after {
  content: '';
  position: absolute; top: 100%; left: 50%;
  transform: translateX(-50%);
  border: 6px solid transparent;
  border-top-color: var(--bg-elevated);
}

.tooltip-wrapper:hover .tooltip,
.tooltip-wrapper:focus-within .tooltip {
  opacity: 1; visibility: visible;
}
```
