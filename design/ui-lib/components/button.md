# Button 按钮

> 最后更新：2026-03-30

---

## 设计目标

按钮是用户与产品交互的核心触点。设计需要在视觉层级中清晰表达操作优先级，同时保持品牌一致性。主操作按钮使用品牌渐变色，次要操作使用幽灵样式，减少视觉噪音。

## 变体

| 变体 | 类名 | 用途 | 视觉特征 |
|------|------|------|----------|
| Primary | `.btn--primary` | 主 CTA（生成、提交） | 紫蓝渐变背景，白色文字 |
| Secondary | `.btn--secondary` | 次要操作（取消、重置） | 透明背景，品牌色边框 |
| Ghost | `.btn--ghost` | 弱操作（更多选项） | 透明背景，hover 时显示底色 |
| Danger | `.btn--danger` | 危险操作（删除） | 红色背景或边框 |
| Text | `.btn--text` | 内联操作（展开详情） | 纯文字，下划线 hover |

## 尺寸

| 尺寸 | 类名 | 高度 | 水平内距 | 垂直内距 | 字号 | 圆角 |
|------|------|------|----------|----------|------|------|
| SM | `.btn--sm` | 32px | 12px | 8px | 14px | 4px |
| MD（默认） | `.btn--md` | 40px | 16px | 12px | 14px | 8px |
| LG | `.btn--lg` | 48px | 24px | 16px | 16px | 8px |
| XL | `.btn--xl` | 56px | 32px | 16px | 18px | 12px |

## 状态

| 状态 | 类名 | 视觉表现 |
|------|------|----------|
| Default | — | 正常样式 |
| Hover | `:hover` | 背景色微亮，上移 1px，阴影加深 |
| Active | `:active` | 下移 1px，阴影缩小 |
| Focus | `:focus-visible` | 2px 品牌色 outline，偏移 2px |
| Disabled | `.is-disabled` / `:disabled` | 透明度 0.5，cursor: not-allowed |
| Loading | `.is-loading` | 显示 spinner，文字变淡，不可点击 |

## 图标按钮

支持在按钮内嵌入图标（左侧、右侧、或仅图标）：

```html
<!-- 左侧图标 -->
<button class="btn btn--primary btn--md">
  <svg class="btn__icon btn__icon--left">...</svg>
  生成作品
</button>

<!-- 仅图标 -->
<button class="btn btn--ghost btn--icon">
  <svg class="btn__icon">...</svg>
</button>
```

仅图标按钮尺寸：32px / 36px / 40px，保持方形比例。

## 可访问性

- 触摸目标最小 44x44px（SM 尺寸仅桌面端使用）
- `:focus-visible` 显示 2px outline，不使用 `:focus` 以避免鼠标点击时闪烁
- Loading 状态需设置 `aria-busy="true"` 和 `aria-disabled="true"`
- 图标按钮必须有 `aria-label`

## 代码示例

```html
<!-- Primary 大按钮 -->
<button class="btn btn--primary btn--lg">
  开始生成
</button>

<!-- Secondary 中按钮 + 左图标 -->
<button class="btn btn--secondary btn--md">
  <svg class="btn__icon btn__icon--left" width="16" height="16" viewBox="0 0 16 16" fill="none">
    <path d="M2 8h12M9 3l5 5-5 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </svg>
  上传图片
</button>

<!-- 危险操作 -->
<button class="btn btn--danger btn--md">
  删除作品
</button>

<!-- Loading 状态 -->
<button class="btn btn--primary btn--md is-loading" aria-busy="true" aria-disabled="true">
  <span class="btn__spinner" role="status"></span>
  生成中...
</button>
```

## CSS 规范

```css
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--spacing-2);
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  font-weight: var(--font-semibold);
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  border: none;
  border-radius: var(--radius-md);
  transition:
    background-color var(--transition-fast),
    box-shadow var(--transition-fast),
    transform var(--transition-fast),
    opacity var(--transition-fast);
  user-select: none;
  text-decoration: none;
}

/* Focus — 仅键盘 */
.btn:focus-visible {
  outline: 2px solid var(--color-brand-mid);
  outline-offset: 2px;
}

.btn:focus:not(:focus-visible) {
  outline: none;
}

/* Primary */
.btn--primary {
  background: var(--color-brand-gradient);
  color: #FFFFFF;
}

.btn--primary:hover:not(:disabled) {
  box-shadow: var(--shadow-brand);
  transform: translateY(-1px);
}

.btn--primary:active:not(:disabled) {
  transform: translateY(1px);
  box-shadow: none;
}

/* Disabled */
.btn:disabled,
.btn.is-disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}
```

## 设计决策

1. **主按钮使用品牌渐变而非纯色** — 强化品牌识别度，与竞品（纯蓝/纯紫）形成差异
2. **Hover 上移 1px 而非放大** — 避免布局抖动，保持界面稳定
3. **Loading 状态文字保留** — 告知用户正在进行的操作是什么
4. **Secondary 使用边框而非浅色填充** — 在深色主题下更清晰，层级更明确
