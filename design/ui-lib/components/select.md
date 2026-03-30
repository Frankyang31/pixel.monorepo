# Select 下拉选择

> 最后更新：2026-03-30

---

## 设计目标

Select 用于从预设选项中选择一个值，核心使用场景包括风格预设、分辨率选择、模型选择等。保持与 Input 一致的视觉语言，下拉面板使用深色（Light 主题下）或更深色（Dark 主题下）背景。

## 变体

| 变体 | 类名 | 用途 |
|------|------|------|
| Default | `.select` | 标准下拉选择 |
| With Group | `.select-group` | 带分组标题 |

## 尺寸

与 Input 一致：SM (32px) / MD (40px) / LG (48px)

## 状态

Default / Hover / Focus / Error / Disabled（同 Input）

## 结构

```html
<div class="select-wrapper">
  <select class="select" aria-label="选择风格">
    <option value="">选择风格预设</option>
    <option value="fantasy-rpg">奇幻 RPG</option>
    <option value="anime">二次元</option>
    <option value="pixel">像素风</option>
  </select>
  <svg class="select__chevron" aria-hidden="true">...</svg>
</div>
```

## 可访问性

- 使用原生 `<select>` 元素（非自定义 div），天然支持键盘和屏幕阅读器
- Label 关联 `for` 属性
- 下拉箭头标记 `aria-hidden="true"`
- 必填时加 `required` + `aria-required="true"`

## 设计决策

1. **使用原生 select 而非自定义** — 原生元素开箱即用的键盘导航、屏幕阅读器支持、移动端体验
2. **自定义箭头图标** — 隐藏默认箭头，使用品牌色 chevron 保持视觉一致性
3. **下拉面板由浏览器控制** — 不同操作系统有各自的下拉交互模式，尊重平台习惯
