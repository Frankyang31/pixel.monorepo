# Input 输入框

> 最后更新：2026-03-30

---

## 设计目标

输入框是表单交互的基础组件。需要在视觉上融入整体设计语言，同时提供清晰的输入引导和状态反馈。深色主题下使用发光效果表达聚焦状态，而非传统蓝色边框。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default | `.input` | 标准文本输入 |
| Search | `.input--search` | 搜索框，左侧搜索图标，右侧清除按钮 |
| With Prefix | `.input-wrapper--prefix` | 带 ￥、比例等前缀 |
| With Suffix | `.input-wrapper--suffix` | 带 px、%、单位等后缀 |

## 尺寸

| 尺寸 | 类名 | 高度 | 内距 | 字号 |
|------|------|------|------|------|
| SM | `.input--sm` | 32px | 6px 10px | 12px |
| MD（默认） | `.input--md` | 40px | 10px 14px | 14px |
| LG | `.input--lg` | 48px | 12px 16px | 16px |

## 状态

| 状态 | 类名 | 视觉表现 |
|------|------|----------|
| Default | — | 底色 `--bg-overlay`，边框 `--border-default` |
| Hover | `:hover` | 边框变为 `--border-strong` |
| Focus | `:focus` | 品牌色边框 `--border-brand`，深色模式品牌光晕 |
| Error | `.is-error` | 红色边框 `--color-error`，错误提示文字 |
| Success | `.is-success` | 绿色边框 `--color-success` |
| Disabled | `:disabled` | 透明度 0.5，cursor: not-allowed |
| Readonly | `[readonly]` | 正常显示，不可编辑 |

## 结构

```html
<div class="input-group">
  <label class="input-label" for="name">名称</label>
  <div class="input-wrapper">
    <span class="input-prefix">$</span>
    <input class="input" id="name" type="text" placeholder="请输入...">
    <span class="input-suffix">px</span>
  </div>
  <span class="input-hint">提示文字</span>
</div>
```

## 带 Label 整体高度

- 输入框：40px
- Label 与输入框间距：6px
- 整体高度：约 72px（含 label + gap + input）

## 可访问性

- Label 使用 `<label>` 并通过 `for` 关联 input `id`
- 错误状态使用 `aria-invalid="true"` + `aria-describedby` 关联错误消息
- 必填字段 label 加 `*` 并设置 `aria-required="true"`
- 搜索框清除按钮添加 `aria-label="清除搜索"`

## 代码示例

```html
<!-- 标准输入框 -->
<div class="input-group">
  <label class="input-label" for="username">用户名 <span class="input-required">*</span></label>
  <input class="input input--md" id="username" type="text" placeholder="请输入用户名" required aria-required="true">
</div>

<!-- 搜索框 -->
<div class="input-wrapper input-wrapper--search">
  <svg class="input-search-icon" ...></svg>
  <input class="input" type="search" placeholder="搜索作品..." aria-label="搜索作品">
  <button class="input-clear-btn" aria-label="清除搜索">&times;</button>
</div>

<!-- 错误状态 -->
<div class="input-group">
  <label class="input-label" for="email">邮箱</label>
  <input class="input is-error" id="email" type="email" aria-invalid="true" aria-describedby="email-error">
  <span class="input-hint input-hint--error" id="email-error">请输入有效的邮箱地址</span>
</div>
```

## CSS 规范

```css
.input {
  width: 100%;
  height: 40px;
  padding: 0 var(--spacing-3);
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  color: var(--text-primary);
  background: var(--bg-overlay);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-md);
  transition: border-color var(--transition-fast), box-shadow var(--transition-fast);
}

.input::placeholder {
  color: var(--text-tertiary);
}

.input:hover:not(:disabled) {
  border-color: var(--border-strong);
}

.input:focus {
  outline: none;
  border-color: var(--border-brand);
  box-shadow: 0 0 0 3px rgba(123, 94, 240, 0.12);
}

/* 深色主题品牌光晕 */
[data-theme="dark"] .input:focus {
  box-shadow: 0 0 20px rgba(123, 94, 240, 0.15);
}

.input.is-error {
  border-color: var(--color-error);
}

.input.is-error:focus {
  box-shadow: 0 0 0 3px rgba(248, 113, 113, 0.12);
}
```

## 设计决策

1. **聚焦使用品牌紫色而非蓝色** — 与整体品牌调性统一
2. **深色主题聚焦加发光** — 利用发光效果替代传统投影，增强「工作室」沉浸感
3. **错误使用红色边框而非红色背景** — 背景变红过于强烈，边框足以传达错误信息且不干扰阅读
4. **Placeholder 使用 tertiary 色** — 视觉层级低于输入文字，引导用户填入真实内容
