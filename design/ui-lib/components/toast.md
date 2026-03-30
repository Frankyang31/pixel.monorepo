# Toast 轻提示

> 最后更新：2026-03-30

---

## 设计目标

Toast 用于操作后的非阻断式反馈，如"保存成功"、"复制成功"、"积分不足"等。自动消失，不打断用户操作流。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default/Info | `.toast` | 信息提示 |
| Success | `.toast--success` | 成功反馈 |
| Warning | `.toast--warning` | 警告提示 |
| Error | `.toast--error` | 错误提示 |

## 尺寸

| 属性 | 值 |
|------|-----|
| 最大宽度 | 380px |
| 内距 | 12px 16px |
| 字号 | 14px |
| 圆角 | 12px |
| 图标尺寸 | 20px |

## 位置

固定在视口右上角（`top: 24px; right: 24px`），多个 Toast 垂直堆叠，间距 8px。

## 行为

- 入场：从右侧滑入 + fade，300ms `ease-out`
- 停留：默认 3 秒（Success 2 秒，Error 5 秒）
- 退场：向右滑出 + fade，200ms `ease-in`
- 可手动关闭（点击关闭按钮）
- 最多同时显示 3 条，超出时最早的自动消失

## 结构

```html
<div class="toast toast--success" role="alert" aria-live="assertive">
  <svg class="toast__icon">...</svg>
  <div class="toast__content">
    <div class="toast__title">生成完成</div>
    <div class="toast__desc">角色立绘已保存到我的作品集</div>
  </div>
  <button class="toast__close" aria-label="关闭">&times;</button>
</div>
```

## 可访问性

- `role="alert"` 用于需要立即关注的信息
- `aria-live="assertive"` 立即通知屏幕阅读器
- 关闭按钮需要 `aria-label`
- 3 秒后自动消失前需要确保屏幕阅读器已读出内容

## CSS 规范

```css
.toast-container {
  position: fixed;
  top: var(--spacing-6);
  right: var(--spacing-6);
  z-index: var(--z-toast);
  display: flex;
  flex-direction: column;
  gap: var(--spacing-2);
}

.toast {
  display: flex;
  align-items: flex-start;
  gap: var(--spacing-3);
  padding: var(--spacing-3) var(--spacing-4);
  background: var(--bg-surface);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-lg);
  max-width: 380px;
  animation: toastIn 300ms var(--ease-out);
}

.toast__icon { width: 20px; height: 20px; flex-shrink: 0; margin-top: 1px; }
.toast--success .toast__icon { color: var(--color-success); }
.toast--warning .toast__icon { color: var(--color-warning); }
.toast--error .toast__icon { color: var(--color-error); }
```
