# Icon 图标

> 最后更新：2026-03-30

---

## 设计目标

图标系统用于传达操作意图、增强视觉识别。所有图标采用线性（outline）风格，保持一致的视觉语言。支持自定义尺寸和颜色，颜色继承自父元素 `currentColor`。

## 设计规范

| 属性 | 值 |
|------|-----|
| 风格 | 线性（outline），描边图标 |
| 默认尺寸 | 16px / 20px / 24px |
| 描边宽度 | 1.5px（16px）/ 1.5px（20px）/ 1.5px（24px） |
| 圆角 | `stroke-linecap: round`，`stroke-linejoin: round` |
| 颜色 | 继承 `currentColor`，不填充 |
| 视觉盒 | 16/20/24px 等宽等高 viewBox |
| 网格对齐 | 基于 2px 网格（24px 图标）或 1px 网格（16px 图标） |

## 尺寸类

| 类名 | 尺寸 | 用途 |
|------|------|------|
| `.icon--xs` | 12px | 紧凑列表、Badge 内 |
| `.icon--sm` | 16px | 按钮内、表格操作列 |
| `.icon--md` | 20px | 导航栏、卡片标题 |
| `.icon--lg` | 24px | 空状态、功能入口 |
| `.icon--xl` | 32px | 展示区域 |

## 使用方式

### SVG 内联（推荐）

```html
<svg class="icon icon--md" viewBox="0 0 20 20" fill="none" aria-hidden="true">
  <path d="M..." stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
```

### 图标 + 文字

```html
<button class="btn btn--primary">
  <svg class="icon icon--sm" viewBox="0 0 16 16" fill="none" aria-hidden="true">
    <path d="M8 1.5v13M1.5 8h13" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
  </svg>
  新建任务
</button>
```

### 独立可交互图标

```html
<button class="btn btn--ghost btn--icon" aria-label="设置">
  <svg class="icon icon--md" viewBox="0 0 20 20" fill="none" aria-hidden="true">
    <path d="M..." stroke="currentColor" stroke-width="1.5"/>
  </svg>
</button>
```

## 分类图标清单

### 导航类
| 名称 | 描述 |
|------|------|
| home | 首页 |
| tools | 工具/创作 |
| gallery | 作品广场 |
| profile | 个人中心 |
| settings | 设置 |

### 操作类
| 名称 | 描述 |
|------|------|
| plus | 新建/添加 |
| search | 搜索 |
| filter | 筛选 |
| download | 下载 |
| upload | 上传 |
| share | 分享 |
| edit | 编辑 |
| delete | 删除 |
| copy | 复制 |
| more | 更多（三点） |

### 状态类
| 名称 | 描述 |
|------|------|
| check | 成功/确认 |
| close | 关闭 |
| info | 信息 |
| warning | 警告 |
| error | 错误 |

### 业务类
| 名称 | 描述 |
|------|------|
| character | 角色 |
| scene | 场景 |
| sprite | Sprite |
| animation | 动画 |
| texture | 纹理 |
| credits | 积分 |

## 可访问性

- 装饰性图标设置 `aria-hidden="true"`
- 交互性图标（按钮内、链接内）由父元素提供 `aria-label`
- 独立图标按钮必须有 `aria-label`
- 图标不使用 `<title>` 标签（Screen reader 重复朗读问题）

## 设计决策

1. **仅使用线性描边风格** — 不混用填充和描边，保持视觉一致性
2. **统一 stroke-width 1.5px** — 在各尺寸下保持一致的粗细感知
3. **currentColor 继承** — 颜色由 CSS 控制，无需修改 SVG
4. **SVG 内联而非 sprite** — 便于按需加载和样式控制，HTTP/2 下性能足够
