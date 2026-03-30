# AI 内容生成平台 — 组件库 v2.0 "深夜工作室"

> 版本 2.0 | 基于设计规范 v1.0 + 品牌差异化设计理念
> 最后更新：2026-03-30

---

## 设计理念

我们不是在做一个 SaaS 后台管理系统，而是在做一个**创作者的沉浸式工具**。组件的终极目标：让界面安静退到背景，让用户的作品成为唯一的主角。

完整设计理念文档：[DESIGN-PHILOSOPHY.md](DESIGN-PHILOSOPHY.md)

### 核心设计语言

**氛围感优先** — 发光替代投影，玻璃质感营造层次，渐变即品牌色。当你打开工作台，感觉像是走进了一间灯光柔和的深夜工作室。

**交互即反馈** — 聚焦光晕从边框向外扩散，按钮弹性按压回弹，渐变边框旋转替代传统 spinner。让用户感知到系统是活的。

**克制的力量** — 留白是呼吸，颜色做减法，严格三级文字层级。

### v1 → v2 差异化要点

| 维度 | v1.0 | v2.0 "深夜工作室" |
|------|------|-------------------|
| 层次表达 | 传统投影 | 发光（glow）+ 玻璃模糊 |
| 输入框背景 | 实色 `#252535` | 玻璃态 `rgba + backdrop-blur` |
| Focus 反馈 | 描边变色 | 品牌色光晕扩散 |
| 选中态 | 边框变色 | 渐变边框 + 外发光 |
| Loading | 旋转 spinner | 渐变边框旋转（conic-gradient） |
| Tab 指示器 | 实色下划线 | 品牌渐变色条 + 渐变文字 |
| Modal 遮罩 | 半透明黑 | 模糊遮罩 + 玻璃态面板 |
| Toast | 简单浮层 | 玻璃态 + 左侧渐变色条 + 滑入动画 |
| Stats 数字 | 纯色文字 | 品牌渐变色（background-clip: text） |

## 文件结构

```
design/ui-lib/
├── README.md              ← 组件库总览文档
├── DESIGN-PHILOSOPHY.md   ← 设计理念文档（宪法级参考）
├── tokens.css             ← Design Tokens v2.0（含发光/玻璃/渐变旋转令牌）
├── preview.html           ← 组件库总览导航页
└── components/
    ├── button.html        ← Button 按钮（发光行动召唤）
    ├── input.html         ← Input 输入框（安静画框 + Prompt 创作画布）
    ├── card.html          ← Card 卡片（漂浮画框 + 画廊模式）
    ├── tabs.html          ← Tabs 选项卡（安静导航灯）
    ├── badge.html         ← Badge 徽章（发光标签）
    ├── modal.html         ← Modal 弹窗（画布浮窗）
    └── toast.html         ← Toast 通知（滑入光条）
```

## 核心组件

| 组件 | 定位 | 差异化特性 |
|------|------|-----------|
| **Button** | 发光的行动召唤 | 渐变光晕 hover、弹性按压 `scale(0.97)`、渐变边框旋转（`conic-gradient`）、底部进度条 |
| **Input** | 安静的画框 | 玻璃态背景、品牌色聚焦光晕、Prompt 创作画布（左侧渐变竖线 + mono 字体） |
| **Card** | 漂浮的画框 | 渐变选中边框（mask-composite 技巧）、画廊模式（底部渐变蒙版）、渐变数字 |
| **Tabs** | 安静的导航灯 | 渐变指示器、活跃项渐变文字（`background-clip: text`）、玻璃态 Pill/Segment |
| **Badge** | 发光的标签 | 玻璃态功能色底色、脉冲圆点呼吸灯、渐变文字标签、发光计数角标 |
| **Modal** | 画布上的浮窗 | `backdrop-filter: blur(8px)` 遮罩、玻璃态面板、顶部品牌色渐变光线 |
| **Toast** | 滑入的光条 | 玻璃态卡片、左侧渐变色条、右侧滑入动画、自动消失进度条 |

## Design Tokens v2.0 新增令牌

```css
/* 发光效果 */
--glow-brand-sm / --glow-brand-md / --glow-brand-lg
--glow-success / --glow-error

/* 玻璃质感 */
--glass-bg / --glass-bg-subtle / --glass-bg-strong
--glass-blur / --glass-blur-heavy
--glass-border

/* Focus 光环 */
--focus-ring

/* 遮罩 */
--overlay-backdrop / --overlay-blur

/* 渐变文字 */
--text-gradient / --text-gradient-color（fallback）

/* 全局动画 */
--glow-rotate（渐变边框旋转 2.5s linear infinite）
pulse-glow / float-in-up / slide-in-right / progress-fill / shimmer
```

## 快速开始

### 引入设计令牌

```html
<link rel="stylesheet" href="tokens.css">
```

### 切换主题

```html
<html data-theme="dark">  <!-- 深色（主战场） -->
<html data-theme="light"> <!-- 浅色（营销场景） -->
```

### 浏览组件

打开 `preview.html` 总览页，点击任意组件卡片跳转到独立交互预览。

## 命名规范

BEM 风格：`.block` → `.block--modifier` → `.block__element` → `.block.is-state`

## 不做的清单

- 粒子效果（DOM 负担，与"界面退到背景"冲突）
- 彩虹渐变（限定品牌紫蓝色域）
- 3D 变换（保持平面感）
- 霓虹文字发光（对比度差，影响可访问性）
- 过度模糊（backdrop-blur 上限 20px）

## 变更日志

### v2.0 (2026-03-30)
- 全面重构为"深夜工作室"设计语言
- Design Tokens v2.0：新增发光、玻璃质感、渐变边框旋转等令牌
- 新增设计理念文档 DESIGN-PHILOSOPHY.md
- 8 个核心组件重写：Button / Input / Card / Tabs / Badge / Modal / Toast
- 差异化特性：渐变边框旋转、Prompt 创作画布、渐变选中边框、脉冲圆点等
- 深色主题作为默认展示（产品主战场）

### v1.0 (2026-03-30)
- 初始组件库发布（已归档）
- 14 个基础组件（6 个核心 + 8 个辅助）
- Light / Dark 双主题支持
