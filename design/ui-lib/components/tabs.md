# Tabs 选项卡

> 最后更新：2026-03-30

---

## 设计目标

Tabs 用于在同一层级内切换不同内容面板，核心使用场景包括工作台模式切换（文生图/图生视频）、作品分类筛选等。需要在视觉上清晰标识当前激活项，同时保持紧凑不占空间。

## 变体

| 变体 | 类名 | 用途 | 视觉特征 |
|------|------|------|----------|
| Underline | `.tabs--underline` | 内容切换（默认） | 底部横线指示器 |
| Pill | `.tabs--pill` | 筛选、模式选择 | 圆角胶囊背景 |
| Segment | `.tabs--segment` | 紧凑切换（2-3项） | 分段控件 |

## 尺寸

| 尺寸 | 类名 | 高度 | 字号 | 内距 |
|------|------|------|------|------|
| SM | `.tabs--sm` | 32px | 12px | 8px 12px |
| MD（默认） | `.tabs--md` | 40px | 14px | 12px 16px |
| LG | `.tabs--lg` | 48px | 16px | 12px 20px |

## 状态

| 状态 | 视觉表现 |
|------|----------|
| Active | 品牌色指示器（下划线/背景），文字加粗 |
| Inactive | 默认文字色，hover 时微亮 |
| Disabled | 透明度 0.4，不可交互 |

## 结构

```html
<div class="tabs tabs--underline" role="tablist">
  <button class="tabs__item is-active" role="tab" aria-selected="true" aria-controls="panel-1">
    角色立绘
  </button>
  <button class="tabs__item" role="tab" aria-selected="false" aria-controls="panel-2">
    场景原画
  </button>
  <button class="tabs__item" role="tab" aria-selected="false" aria-controls="panel-3">
    Sprite 批量
  </button>
</div>
<div class="tabs__panel" id="panel-1" role="tabpanel">...</div>
```

## 可访问性

- 使用 `role="tablist"` / `role="tab"` / `role="tabpanel"` 语义
- 激活 tab 设置 `aria-selected="true"` 并通过 `aria-controls` 关联面板
- 支持键盘导航：← → 切换 tab，Home / End 跳转首末项
- Tab panel 使用 `tabindex="0"` 使其可聚焦

## 带图标

Tab 项内可嵌入小图标（16px），与文字间距 6px：

```html
<button class="tabs__item is-active">
  <svg width="16" height="16">...</svg>
  角色立绘
</button>
```

## 设计决策

1. **默认使用 Underline 样式** — 比 Pill 更轻量，适合内容区域切换，视觉干扰更小
2. **Pill 用于筛选** — 提供更明显的选中区域，适合工具栏或模式切换
3. **Segment 控件限制 2-3 项** — 超过 3 项使用 Underline 或 Pill，避免拥挤
4. **指示器使用品牌渐变** — 保持品牌一致性
