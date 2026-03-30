# AI 游戏资产生成 — Prompt 模板库

> 整理于 2026-03-25，配合 6 个核心工具使用
> 适用于独立游戏开发者、小型工作室，覆盖主流 2D 游戏类型
> 注：英文 prompt 在大多数模型中表现更稳定，中文 prompt 可叠加使用增强本地化风格

---

## 一、角色立绘（Character Art）

### 工具说明
生成角色正面 + 侧面双视角立绘图，输出透明背景 PNG，可直接导入 Spine、RPG Maker 等工具。
**核心参数**：画风（Style）、视角（View）、比例（Proportions）

### Prompt 结构
```
[种族/职业] [性别] character design, [服装描述], [武器/道具],
[画风], front view + side view, transparent PNG,
[细节控制], clean lineart, game asset, 2D game art
```

### 类型模板

#### RPG / 奇幻角色
```
Elven ranger character design, female, leather armor with green accents,
longbow on back, pointed ears, silver hair in braided style,
sword and shield at hip, cel shading style,
front view + side view, transparent PNG, clean lineart,
game asset, 2D RPG art, high detail
```
**配套画风标签**：`#赛璐璐` `#厚涂` `#日式二次元` `#写实风格`

#### 二游 / 抽卡角色
```
Beautiful female warrior gacha character design, flowing dress with blue
and gold gradient fabric, intricate jewelry, long flowing hair with wind
effect, elegant pose with sword raised, anime style with soft shading,
front view + side view, transparent PNG, clean lineart,
game asset, mobile gacha RPG art, highly detailed face
```
**配套画风标签**：`#二次元` `#厚涂` `#日系插画`

#### SLG / 军事策略
```
Knight commander character design, heavy plate armor with royal blue and
silver trim, cape with faction emblem, battle-worn helmet under arm,
stoic expression, realistic style, front view + side view,
transparent PNG, clean lineart, game asset, 2D strategy game art
```
**配套画风标签**：`#写实风格` `#厚涂` `#低多边形`

#### 像素游戏 / 复古风格
```
Retro pixel art warrior character, 16-bit style, green tunic,
leather boots, short sword, side view, transparent background,
game asset, pixel art RPG, limited color palette, 16 colors
```
**注意**：像素风格建议在参数中选择 `pixel-art` 或 `8-bit` 标签，而非写在 prompt 中，以免模型混淆。

### 常见画风标签对照
| 画风 | 英文标签 | 适用场景 |
|------|---------|---------|
| 赛璐璐 | `cel shading`, `flat colors`, `anime` | 日系 RPG、二游 |
| 厚涂 | `oil painting style`, `painterly`, `rich textures` | 写实卡牌、SLG |
| 水墨风 | `Chinese ink painting style`, `sumi-e` | 国风游戏、武侠 |
| 像素风 | `pixel art`, `8-bit`, `16-bit` | 复古像素游戏 |
| Q版 | `chibi style`, `cute proportions` | 休闲游戏、抽卡立绘 |

---

## 二、场景原画（Environment Art）

### 工具说明
生成游戏场景氛围图、过场背景、地图底图，支持宽幅比例（16:9、21:9）。输出可用于远景层、宣传图或概念参考。
**核心参数**：比例（Aspect Ratio）、天气/时间（Time of Day）、风格（Art Style）

### Prompt 结构
```
[地点/环境类型], [天气/时间], [光照描述],
[氛围关键词], [画风], wide shot / full scene,
[细节层次], game background, 2D game art
```

### 类型模板

#### RPG 主城 / 城镇
```
Fantasy medieval town square, morning golden hour light, cobblestone
streets, wooden market stalls with colorful awnings, fountain at center,
busy but peaceful atmosphere, soft shadows, painterly style,
wide shot, full scene, game background, 2D RPG art, highly detailed
```
**比例建议**：16:9
**配套标签**：`#日式奇幻` `#欧美奇幻` `#写实风格`

#### SLG 战场 / 策略地图
```
Strategic battle map, overhead angled view, lush green plains with river
snaking through center, small wooden bridges, scattered pine forests on
north edge, clear blue sky with wispy clouds, muted color palette,
tactical map style, game background, 2D strategy game art, clean and readable
```
**比例建议**：16:9、4:3
**配套标签**：`#军事地图` `#俯视角` `#简约风格`

#### 二游 过场 / 剧情背景
```
Dreamy anime style forest clearing, afternoon dappled sunlight filtering
through cherry blossom trees, floating petals, mystical atmosphere,
soft bokeh light rays, anime background art style,
wide shot, full scene, game background, visual novel art, vibrant colors
```
**比例建议**：16:9、21:9（宽幅电影感）
**配套标签**：`#日系动漫` `#柔光` `#樱花/森林`

#### 像素游戏 地图底图
```
Pixel art game world map, top-down view, grasslands bordering a forest
to the north and desert to the south, winding path connecting three
villages, small lake in center, 16-bit retro style,
pixel art map, game background, limited color palette, 32 colors
```

#### 国风 / 武侠场景
```
Ancient Chinese mountain temple at dawn, misty atmosphere, traditional
Chinese architecture with curved roofs, stone steps leading up,
wisteria trees in bloom, soft warm morning light through mist,
Chinese ink wash painting style blending with watercolor,
wide shot, game background, 2D game art, serene mood
```
**配套标签**：`#水墨风` `#国风` `#武侠`

---

## 三、动画预览（Motion Preview）

### 工具说明
图生视频工具，输入角色立绘图 + 动作描述，输出 2–8 秒 MP4/WebM 动画预览。核心用途是让策划/美术快速验证动作节奏，无需 3D 绑定即可预览角色动画。
**核心参数**：输入图片、动作描述、时长、运动强度

### Input 结构（图片 + 文字）
- **图片输入**：角色立绘图（透明背景 PNG 优先，模型更容易识别角色轮廓）
- **动作描述**：
  - 基础待机：`idle animation, gentle breathing, subtle cloth movement`
  - 行走循环：`walking animation, smooth walk cycle, 2D sprite sheet`
  - 技能释放：`casting spell, magic circle appears, dramatic energy burst`
  - 攻击动作：`sword slash, fast attack motion, impact frame`
  - 转身/转身攻击：`turn around, quick dodge, evasive maneuver`

### 类型模板

#### 角色待机动画
```
Input: 角色立绘图（透明背景 PNG）
Action: idle breathing, gentle sway, cloth moving with wind
Style: smooth, loopable, game sprite animation
Duration: 3 seconds
Motion intensity: light
```
**应用场景**：战斗等待、过场间隙

#### 技能特效动画
```
Input: 手持法杖的角色立绘图
Action: raise staff, magic circle appears beneath feet, energy flows
from staff tip, powerful spell casting pose, dramatic lighting
Style: dynamic, impactful, anime attack animation
Duration: 5 seconds
Motion intensity: high
```
**应用场景**：技能预览、战斗特效验证

#### 行走循环动画
```
Input: 全身角色立绘图，战斗姿态
Action: walking forward, slight bounce, weapon at ready, cape flowing,
smooth loop
Style: game-ready, 2D sprite walk cycle
Duration: 4 seconds
Motion intensity: medium
```
**应用场景**：角色移动预览、场景穿梭

### 注意事项
- 动画预览为 **预览质量**，非最终交付质量（后者需要专业动画师精修）
- 输入图片分辨率越高，生成效果越稳定（建议 1024px 以上）
- 透明背景 PNG 比白底 JPG 效果更好
- 目前最长支持 8 秒，更长时长可分段生成后拼接

---

## 四、Sprite 批量（Sprite Sheet）

### 工具说明
批量生成游戏 UI 图标、道具图标、状态图标，支持多尺寸同比例输出，自动生成合并版 sprite sheet。
**核心参数**：图标类型、尺寸（64/128/256px）、风格统一参数

### Prompt 结构
```
[物品名称], [物品材质], [游戏风格], [尺寸标注],
icon design, flat style / pixel art, clean outlines,
transparent background, game UI, high contrast, [颜色限定]
```

### 类型模板

#### RPG 道具图标
```
Health potion icon, glass bottle with red glowing liquid, cork stopper,
sparkle particles, RPG game style, clean outlines, pixel art,
transparent background, game UI icon, 64x64, high contrast
```
```
Mana crystal icon, blue gem with inner light, floating particles,
fantasy RPG style, clean outlines, pixel art, transparent background,
game UI icon, 64x64, high contrast
```
```
Iron sword icon, metallic blade, leather-wrapped hilt, worn battle look,
fantasy RPG style, clean outlines, pixel art, transparent background,
game UI icon, 64x64, high contrast
```

#### SLG 兵种图标
```
Cavalry unit icon, knight on horseback silhouette, heraldic shield emblem,
strategy game style, clean military icon, flat design, pixel art,
transparent background, game UI icon, 64x64, high contrast
```
```
Archer unit icon, bow drawn, arrow nocked, hooded archer silhouette,
strategy game style, clean military icon, flat design, pixel art,
transparent background, game UI icon, 64x64, high contrast
```

#### 二游 技能状态图标
```
Fire buff state icon, flames wrapping around circular frame, orange and
red glow, anime game style, clean icon design, glowing effect,
transparent background, game UI icon, 128x128, high saturation
```
```
Shield defense buff icon, blue energy shield hexagonal frame, crystalline
surface, anime game style, clean icon design, glowing effect,
transparent background, game UI icon, 128x128, high saturation
```
```
Poison debuff state icon, skull with green mist, venom drops, dark anime
game style, ominous icon design, transparent background,
game UI icon, 128x128, high contrast
```

### 批量生成技巧
- **风格一致性**：首先生成 1 个满意样本，记录 seed 值，后续批量图使用相同 seed，仅改变物品名
- **尺寸选择**：UI 图标选 64px，道具大图选 128px，宣传图标选 256px
- **色彩限定**：如需统一调色板，可在 prompt 末尾加 `--color-palette 8-colors`

---

## 五、材质纹理（Texture Generator）

### 工具说明
基于参考图进行风格重绘或细节增强，典型场景：低分辨率资产升频（upscale）、3D 模型 UV 贴图生成 PBR 材质纹理、统一已有资产画风。
**核心参数**：输入图片、目标风格、纹理类型、升频倍数

### 使用场景

#### 场景 A：低分辨率升频 + 细节增强
**输入**：256px 的道具图标
**Prompt**：
```
Upscale to high resolution, enhance texture details, clean crisp edges,
preserve original color palette, pixel art style, game-ready texture,
2x resolution boost, remove jpeg artifacts, sharpen details
```

#### 场景 B：PBR 材质纹理生成
**输入**：基础石墙参考图
**Prompt**：
```
Generate PBR texture map, stone wall with moss between cracks,
albedo map + normal map + roughness map, seamless tileable texture,
realistic style, game-ready, 2K resolution
```

#### 场景 C：画风统一（让 AI 批量图与主角色风格一致）
**输入**：需要统一风格的资产图
**Prompt**：
```
Convert to [目标画风], match cel-shading style, preserve original
composition and subject, anime game art style, clean outlines maintained,
transparent areas preserved
```

### 纹理类型关键词
| 纹理类型 | 关键词 |
|---------|--------|
| 基础色贴图 | `albedo`, `base color`, `diffuse` |
| 法线贴图 | `normal map`, `bump map` |
| 粗糙度贴图 | `roughness map`, `specular map` |
| 环境光遮蔽 | `ambient occlusion`, `AO map` |
| 可平铺纹理 | `seamless tileable`, `pattern repeat` |

---

## 六、细节精修（AI Retouch）

### 工具说明
局部重绘与修复（Inpainting），典型场景：替换角色某个部件、修复 AI 生成图的崩坏部位、添加或移除背景元素。
**核心参数**：蒙版区域（需涂抹要修改的部分）、修复 prompt

### Prompt 结构
```
Replace [目标元素] with [替换内容], keep everything else identical,
maintain [保留特征], consistent with original style,
game asset, 2D art
```

### 类型模板

#### 替换角色装备/服装
**蒙版**：选中角色身上的武器
**Prompt**：
```
Replace sword with staff, wizard staff with glowing crystal top,
wooden shaft, maintain character pose and proportions,
preserve background and other equipment, cel shading style,
game character art
```

#### 修复崩坏的手指
**蒙版**：选中角色手部区域
**Prompt**：
```
Fix hand, correct number of fingers to 5, natural hand pose,
no extra fingers, clean lineart, consistent shading with rest
of image, game character art
```

#### 替换发型
**蒙版**：选中角色头发
**Prompt**：
```
Change hairstyle to long flowing hair with side braid,
maintain face and expression, same hair color,
anime cel shading style, game character art
```

#### 去除背景杂物
**蒙版**：选中背景中的杂物（如水印、多余道具）
**Prompt**：
```
Remove [杂物描述], fill with appropriate background content,
seamless inpainting, maintain lighting and perspective,
preserve character and main subject, game art style
```

### 通用 Negative Prompt（反推提示词）
适用于所有工具，添加到负面提示词框：
```
deformed, extra fingers, extra limbs, bad anatomy,
missing fingers, blurry, low quality, jpeg artifacts,
watermark, text, logo, cropped, worst quality
```

---

## 附录：通用参数建议

### 分辨率建议
| 工具 | 推荐分辨率 | 说明 |
|------|----------|------|
| 角色立绘 | 1024×1024 或 1024×1536 | 立绘通常竖图比例更好 |
| 场景原画 | 1920×1080 或 2560×1440 | 16:9 宽屏 |
| Sprite 批量 | 64×64 / 128×128 / 256×256 | 固定尺寸，按需选择 |
| 动画预览 | 输入 1024px 以上 | 输出 720p MP4 |
| 材质纹理 | 2048×2048 | PBR 贴图标准 |
| 细节精修 | 与原图相同 | 不要升频后再精修 |

### 积分消耗估算
| 工具 | 单次积分 | 批量建议 |
|------|---------|---------|
| 角色立绘 | 3–5 积分（1–4 张） | 先出 1 张，确认风格后批量 |
| 场景原画 | 4–6 积分（1–2 张） | 场景复用率低，建议单独精修 |
| 动画预览 | 15–25 积分 | 预览用，控制时长 |
| Sprite 批量 | 2 积分/张 | 风格确认后批量 20–50 张 |
| 材质纹理 | 3–5 积分 | 单张精修 |
| 细节精修 | 2 积分/次 | 可多次迭代 |

---

*本文档将随产品迭代持续更新，欢迎提交你常用的 Prompt 模板。*
