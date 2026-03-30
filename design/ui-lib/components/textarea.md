# Textarea 文本域

> 最后更新：2026-03-30

---

## 设计目标

Textarea 用于多行文本输入场景，核心使用场景为 AI Prompt 输入。需要在深色主题下营造「创意工作台」氛围，使用代码字体增强专业感，同时提供清晰的字数反馈。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default | `.textarea` | 通用多行文本 |
| Prompt | `.textarea--prompt` | AI Prompt 输入区，使用 mono 字体 |
| Resize None | `.textarea--no-resize` | 禁止拖拽调整大小 |

## 尺寸

| 尺寸 | 类名 | 默认行数 | 内距 | 字号 |
|------|------|----------|------|------|
| SM | `.textarea--sm` | 3 | 8px 12px | 12px |
| MD（默认） | `.textarea--md` | 4 | 12px 14px | 14px |
| LG | `.textarea--lg` | 6 | 14px 16px | 16px |

Prompt 模式固定最小高度 120px，最大 400px，`resize: vertical`。

## 状态

与 Input 一致：Default / Hover / Focus / Error / Disabled

## 结构

```html
<div class="textarea-group">
  <label class="textarea-label" for="prompt">描述你想生成的角色</label>
  <textarea class="textarea textarea--prompt" id="prompt" rows="4"
    placeholder="A female elven ranger with silver hair, wearing ornate leather armor..."
    maxlength="2000"></textarea>
  <div class="textarea-footer">
    <span class="textarea-hint">支持中英文描述</span>
    <span class="textarea-counter">0 / 2000</span>
  </div>
</div>
```

## 可访问性

- Label 关联 `for` 属性
- 字数计数器使用 `aria-live="polite"` 实时播报
- 错误状态同 Input

## 代码示例

```css
.textarea {
  width: 100%;
  padding: var(--spacing-3) var(--spacing-4);
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  color: var(--text-primary);
  background: var(--bg-overlay);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-md);
  resize: vertical;
  min-height: 100px;
  transition: border-color var(--transition-fast), box-shadow var(--transition-fast);
}

.textarea--prompt {
  font-family: var(--font-mono);
  min-height: 120px;
  max-height: 400px;
  line-height: 1.6;
}
```

## 设计决策

1. **Prompt 模式使用 mono 字体** — 开发者/AI 创作者对等宽字体有自然亲近感，提升输入体验
2. **底部 footer 放提示 + 计数器** — 左右布局，不占用额外垂直空间
3. **默认 resize: vertical** — 保留用户自主调整的权利，但限制方向避免水平溢出
