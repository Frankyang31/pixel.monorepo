# Switch 开关

> 最后更新：2026-03-30

---

## 设计目标

Switch 用于布尔值切换，如启用/禁用功能、开启/关闭通知。滑动操作比 Checkbox 更符合"开关"的心理模型。

## 尺寸

| 尺寸 | 类名 | 宽度 | 高度 | 圆点 | 用途 |
|------|------|------|------|------|------|
| SM | `.switch--sm` | 32px | 18px | 14px | 紧凑列表 |
| MD（默认） | `.switch--md` | 44px | 24px | 18px | 设置面板 |
| LG | `.switch--lg` | 56px | 30px | 24px | 独立操作 |

## 状态

| 状态 | 视觉表现 |
|------|----------|
| Off | `--bg-overlay` 背景，灰色圆点 |
| On | 品牌渐变背景，白色圆点，圆点滑动到右侧 |
| Hover | 边框微亮 |
| Focus | 品牌色 outline |
| Disabled | 透明度 0.4 |

## 结构

```html
<label class="switch-label">
  <input type="checkbox" class="switch-input" role="switch">
  <span class="switch-control">
    <span class="switch-thumb"></span>
  </span>
  <span class="switch-text">自动保存</span>
</label>
```

## 可访问性

- 使用 `role="switch"` 语义
- 添加 `aria-checked` 属性
- 支持 Space / Enter 键切换
- Focus 状态使用 `:focus-visible` outline

## 动画

- 圆点移动使用 `transform: translateX()` 而非 `left/right`，GPU 加速
- 过渡时长 200ms，使用 `ease-spring` 产生弹性感
- 尊重 `prefers-reduced-motion`

## 代码示例

```css
.switch-control {
  width: 44px; height: 24px;
  background: var(--bg-overlay);
  border: 1px solid var(--border-default);
  border-radius: var(--radius-full);
  position: relative;
  transition: background-color var(--transition-fast), border-color var(--transition-fast);
}

.switch-thumb {
  position: absolute; top: 2px; left: 2px;
  width: 18px; height: 18px;
  background: white; border-radius: 50%;
  transition: transform 200ms cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: 0 1px 3px rgba(0,0,0,0.2);
}

.switch-input:checked + .switch-control {
  background: var(--color-brand-gradient);
  border-color: transparent;
}

.switch-input:checked + .switch-control .switch-thumb {
  transform: translateX(20px);
}
```
