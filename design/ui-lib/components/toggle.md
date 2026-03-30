# Toggle 按钮组

> 最后更新：2026-03-30

---

## 设计目标

Toggle 用于互斥选项切换（2-5 项），核心场景包括视图切换（网格/列表）、模型选择等。视觉上类似分段控件，但支持更多项。

## 尺寸

| 尺寸 | 类名 | 高度 | 字号 |
|------|------|------|------|
| SM | `.toggle--sm` | 28px | 12px |
| MD（默认） | `.toggle--md` | 36px | 13px |
| LG | `.toggle--lg` | 44px | 14px |

## 状态

| 状态 | 视觉表现 |
|------|----------|
| Inactive | 透明背景，secondary 文字色 |
| Active (Default) | `--bg-surface` 背景，品牌渐变文字，投影 |
| Active (Solid) | 品牌渐变背景，白色文字 |
| Hover (Inactive) | 微亮背景 |
| Disabled | 透明度 0.4 |

## 结构

```html
<div class="toggle" role="radiogroup">
  <button class="toggle__item is-active" role="radio" aria-checked="true">
    <svg ...></svg>
    网格
  </button>
  <button class="toggle__item" role="radio" aria-checked="false">
    <svg ...></svg>
    列表
  </button>
</div>
```

## 可访问性

- `role="radiogroup"` / `role="radio"` 语义
- `aria-checked` 反映选中状态
- 键盘方向键导航（← →）
- Focus 使用 `:focus-visible`

## CSS 规范

```css
.toggle {
  display: inline-flex;
  background: var(--bg-overlay);
  border-radius: var(--radius-md);
  padding: 3px;
  gap: 2px;
}

.toggle__item {
  padding: 0 var(--spacing-3);
  height: 36px;
  border-radius: 6px;
  font-weight: var(--font-medium);
  color: var(--text-secondary);
  background: transparent;
  border: none;
  cursor: pointer;
}

.toggle__item.is-active {
  background: var(--bg-surface);
  color: var(--text-primary);
  box-shadow: var(--shadow-sm);
}
```

## 设计决策

1. **与 Tabs Segment 的区别** — Toggle 用于排他性选项（如模型选择），Tabs 用于内容面板切换
2. **背景使用 surface 而非白色** — 深色主题下避免突兀
3. **支持图标** — 视图切换类 Toggle 内嵌入图标增强识别
