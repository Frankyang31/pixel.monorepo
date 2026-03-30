# Checkbox 复选框

> 最后更新：2026-03-30

---

## 设计目标

Checkbox 用于多选场景，如作品批量操作、偏好设置等。选中状态使用品牌色填充，配合 check 图标和弹性动画反馈。

## 尺寸

| 尺寸 | 类名 | 盒子大小 | 图标 | 用途 |
|------|------|----------|------|------|
| SM | `.checkbox--sm` | 16px | 10px | 紧凑列表、表格 |
| MD（默认） | `.checkbox--md` | 20px | 12px | 表单、设置 |
| LG | `.checkbox--lg` | 24px | 14px | 独立操作区域 |

## 状态

| 状态 | 视觉表现 |
|------|----------|
| Unchecked | `--border-default` 边框，透明背景 |
| Checked | 品牌色渐变背景，白色 check 图标，弹性缩放动画 |
| Indeterminate | 品牌色渐变背景，白色横线 |
| Hover | 边框变为 `--border-strong`，或品牌色微光 |
| Focus | 品牌色 outline |
| Disabled | 透明度 0.4 |

## 结构

```html
<label class="checkbox-label">
  <input type="checkbox" class="checkbox-input" aria-describedby="desc">
  <span class="checkbox-control">
    <svg class="checkbox-check" viewBox="0 0 12 12" fill="none" aria-hidden="true">
      <path d="M2.5 6l2.5 2.5 4.5-5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
  </span>
  <span class="checkbox-text">公开作品</span>
</label>
<span class="checkbox-hint" id="desc">公开后其他用户可在广场看到</span>
```

## 可访问性

- 隐藏原生 input 但保持可聚焦（sr-only）
- 自定义控件使用 `aria-hidden="true"`
- Label 包裹整个区域，点击任意位置均可切换
- Indeterminate 状态通过 JS 设置 `input.indeterminate = true`
- 错误提示通过 `aria-describedby` 关联

## 代码示例

```css
.checkbox-control {
  width: 20px; height: 20px;
  border: 1.5px solid var(--border-default);
  border-radius: var(--radius-sm);
  display: flex; align-items: center; justify-content: center;
  transition: all var(--transition-fast);
  background: transparent;
}

.checkbox-input:checked + .checkbox-control {
  background: var(--color-brand-gradient);
  border-color: transparent;
}

.checkbox-check {
  width: 12px; height: 12px;
  color: white;
  opacity: 0; transform: scale(0);
  transition: all var(--transition-fast);
}

.checkbox-input:checked + .checkbox-control .checkbox-check {
  opacity: 1; transform: scale(1);
}
```
