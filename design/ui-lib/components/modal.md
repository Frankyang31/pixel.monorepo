# Modal 对话框

> 最后更新：2026-03-30

---

## 设计目标

Modal 用于需要用户注意并做出决策的场景，如删除确认、表单提交、信息展示。设计需要阻断背景内容、引导用户聚焦于当前任务，同时提供明确的退出路径。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default | `.modal` | 标准对话框 |
| Danger | `.modal--danger` | 危险操作确认 |
| Fullscreen | `.modal--fullscreen` | 全屏弹窗（移动端） |

## 结构

```html
<div class="modal-overlay" role="dialog" aria-modal="true" aria-labelledby="modal-title">
  <div class="modal">
    <header class="modal__header">
      <h2 class="modal__title" id="modal-title">确认删除</h2>
      <button class="modal__close" aria-label="关闭">
        <svg>...</svg>
      </button>
    </header>
    <div class="modal__body">
      <p>确定要删除这件作品吗？此操作无法撤销。</p>
    </div>
    <footer class="modal__footer">
      <button class="btn btn--secondary">取消</button>
      <button class="btn btn--danger">确认删除</button>
    </footer>
  </div>
</div>
```

## 尺寸

| 变体 | 最大宽度 | 内距 |
|------|----------|------|
| SM | 400px | 24px |
| MD（默认） | 480px | 24px |
| LG | 640px | 32px |

## 行为规范

- 打开：背景渐显（200ms），Modal 从中心 scale + fade 进入（300ms）
- 关闭：反向动画，关闭后焦点返回触发元素
- 点击遮罩层不关闭（防误操作），必须通过按钮或 ESC 关闭
- ESC 键关闭
- 打开时 body 滚动锁定

## 可访问性

- `role="dialog"` + `aria-modal="true"`
- `aria-labelledby` 关联标题
- 焦点陷阱（Tab 不超出 Modal）
- 关闭后焦点返回触发元素
- Escape 关闭

## CSS 规范

```css
.modal-overlay {
  position: fixed; inset: 0;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(4px);
  display: flex; align-items: center; justify-content: center;
  z-index: var(--z-modal);
}

.modal {
  background: var(--bg-surface);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  max-width: 480px;
  width: calc(100% - 48px);
  max-height: calc(100vh - 64px);
  overflow: auto;
}
```
