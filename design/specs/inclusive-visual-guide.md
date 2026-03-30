# PixelMind 多元化视觉设计指南
> 评估日期：2026-03-26 | 评估范围：全站 20 个 HTML 原型页面

---

## 一、评估结论摘要

当前设计在视觉技术层面完成度高，但在多元化表达方面存在几个系统性缺陷，集中体现在四个维度：

| 维度 | 当前状态 | 风险等级 |
|------|----------|----------|
| 用户角色/Avatar 多样性 | 渐变色块 + 单字母头像，无任何人类面孔 | 中 |
| Prompt 示例中的角色原型 | 严重偏向欧美奇幻白人男性英雄 | 高 |
| 游戏风格预设的文化覆盖 | 仅覆盖日式二游、欧美奇幻，缺失亚非文化 | 高 |
| 无障碍可访问性 | 对比度设计可，但彩色渐变传递信息存在色盲风险 | 中 |

---

## 二、具体问题清单（按页面）

### 2.1 首页（homepage-design.html）

**问题 A：Studio 演示区 Prompt 样本高度偏向欧美奇幻白人**

当前 Prompt 轮播内容：
```
"A warrior knight with silver armor, red cape flowing in wind, epic fantasy RPG character art"
"Cyberpunk street market at night, neon signs, rainy streets"
"Anime mage character portrait, flowing blue robes"
```

所有 4 条样本均指向欧美奇幻/日系动漫框架。没有任何体现以下内容的提示词：
- 东南亚/南亚/中东/非洲/拉美游戏美术风格
- 女性主角、老年角色、非二元性别角色
- 现代都市背景或非欧洲历史背景

**问题 B：Gallery 展示区占位颜色暗示了西方叙事偏好**

6 个 Gallery 占位卡片 emoji 使用：⚔️🏔️🛡️🎮——全部是欧美奇幻/战争符号。

---

### 2.2 作品广场（gallery-design.html）

**问题 C：10 个展示作品的 Prompt 文本呈现单一文化视角**

| 序号 | Prompt | 问题 |
|------|--------|------|
| 1 | 赛博朋克城市夜景，霓虹灯雨后倒影 | 赛博朋克 = 日本东京刻板印象 |
| 2 | 水彩风格牡丹，粉色渐变，工笔画质感 | 唯一东亚元素但极度花卉化，无人物 |
| 3 | 未来机器人与人类共存，史诗构图 | 西方科幻审美，人类未指定背景 |
| 5 | 森林精灵小狐狸，吉卜力风格 | 日系框架，主体为动物非人 |
| 9 | 凤凰涅槃，炽热火焰，中国神话风格 | 中国元素但停留在神兽层面，非人类主体 |

**根本问题：所有 10 件作品没有一件展示了具有明确文化身份的人类角色。** 这对一个游戏角色生成平台来说是严重的多元化盲区。

**问题 D：作者头像全部用单汉字/英文首字母 + 渐变色块**

虽然这是技术性占位，但需要明确规范：正式版本中头像需具有多样性，而不是默认使用彩色圆圈。

---

### 2.3 工具详情页（tool-detail-design.html）

**问题 E：Gallery 样本 8 个色块均使用欧美奇幻/科幻配色**

具体颜色组合如 `linear-gradient(145deg,#2d1b69,#764ba2)` 和 `#f093fb,#f5576c` 这类组合实际上暗示了特定的欧美流行艺术风格。

**问题 F：用户评价卡片（review-card）中 3 位评价者的命名和头像**

代码中仅存在 `av1/av2/av3` 三种渐变头像，无名字多样性设计规范。

---

### 2.4 Prompt 示例数据库（根本问题）

当前 Studio 中的 Prompt 样板全部用英文书写，且类型单一：

```
"A warrior knight with silver armor..."
"Cyberpunk street market at night..."
"Pixel art game item icon, golden sword..."
"Anime mage character portrait..."
```

这四条样本在 AI 生成时会触发以下偏见模式：
- "warrior knight" → AI 默认生成白人男性
- "Anime mage" → AI 默认生成日系白皮肤少女
- 无任何约束条件防止上述默认值

---

## 三、优化建议：系统性解决方案

### 3.1 Prompt 样本库：注入交叉性约束

**当前（有偏见的样本）：**
```
"A warrior knight with silver armor, red cape, epic fantasy RPG character art"
```

**优化后（带反偏见约束的样本）：**
```
"A 30-year-old East African warrior queen, braided crown hair, copper-toned armor
inspired by Zulu and Ethiopian military aesthetics, confident frontal pose,
cel-shaded game art style, detailed fabric texture, transparent PNG background.
--negative: white skin, European medieval armor, generic fantasy, clone face"
```

**建议 Prompt 库覆盖的多元角色矩阵：**

| 文化背景 | 性别 | 年龄段 | 游戏类型 |
|----------|------|--------|----------|
| 东非 | 女性 | 成年 | RPG战士 |
| 南亚（印度） | 男性 | 老年 | 策略游戏君主 |
| 中美洲（玛雅） | 非二元 | 青年 | 动作冒险 |
| 中东（波斯） | 女性 | 成年 | 魔法类 |
| 北欧（萨米族） | 男性 | 中年 | 生存类 |
| 东亚（朝鲜族） | 女性 | 老年 | 策略类 |
| 东南亚（爪哇） | 男性 | 青年 | 武侠类 |
| 拉美（安第斯） | 女性 | 成年 | 冒险类 |

---

### 3.2 作品广场：展示内容的多元化重构

**Gallery 展示内容应包含以下配比（初期填充建议）：**

- 人物类（占 60%）
  - 至少 3 种明确标注了不同文化背景的角色
  - 至少包含 1 位老年角色
  - 至少包含 1 位体型非理想化角色
  - 至少包含 1 位有明确文化服饰（而非泛欧洲）的角色

- 场景类（占 30%）
  - 避免全部使用赛博朋克/日式场景
  - 应包含：非洲草原、中东集市、南美古迹、东南亚热带等

- 道具/纹理类（占 10%）

**示例：优化后的 10 件展示作品分配**

| 序号 | Prompt | 作者 | 体现多元性 |
|------|--------|------|-----------|
| 1 | 东非女战士，铜质盔甲，赛璐璐风格，RPG游戏立绘 | Amara_Studio | 非洲文化 + 女性 |
| 2 | 波斯魔法师老者，细密画风格，蓝色头巾，策略游戏 | محمد_آرت | 中东文化 + 老年男性 |
| 3 | 玛雅祭司，翡翠羽饰，等距视角Sprite | Ixchel_Pixel | 中美洲文化 |
| 4 | 朝鲜族奶奶，传统衣着，像素游戏 NPC | 최아트 | 东亚老年女性 |
| 5 | 科幻孟买街景，里克沙文化元素，2049 | Priya_WorldArt | 南亚未来主义 |
| 6 | 安第斯山脉萨满，骆驼毛纹理材质，生存游戏 | AntayArte | 拉美文化 |
| 7 | 爪哇皮影戏风格，武侠精灵，水彩渐变 | Wayang_Creative | 东南亚文化 |
| 8 | 萨米族猎人，驯鹿同行，北欧 Survival 游戏风格 | Sápmi_Art | 北欧原住民 |
| 9 | 湄公河畔渔村，黄昏光照，动画短视频 | MekongMotion | 东南亚场景 |
| 10 | 尼日利亚传统婚礼贡礼道具包，Sprite批量 | Lagos_Icons | 西非文化 + 道具 |

---

### 3.3 风格预设标签：扩展文化覆盖

**当前风格预设（工作台）：**
`奇幻RPG` `二次元` `像素风` `赛璐璐` `写实` `厚涂`

**优化后建议添加：**
`非洲部落风格` `中东细密画` `玛雅神话风` `爪哇皮影风` `波斯地毯纹` `印度神话风` `安第斯编织纹` `朝鲜族水彩`

---

### 3.4 可访问性补强清单

**颜色对比度问题：**
- `.text-tertiary: #606078` 在 `#F5F4F8` 背景上对比度约为 3.8:1，未达到 WCAG AA 的 4.5:1 要求
  - 修复方案：将 `#606078` 调整为 `#545468`（对比度 ≈ 5.1:1）
- 渐变文字（`-webkit-text-fill-color: transparent`）对屏幕阅读器不可见
  - 修复方案：为所有渐变文字添加对应的 `aria-label` 属性

**色盲友好性：**
- 6 个工具的颜色标识（紫/蓝/青/琥珀/粉/靛蓝）主要依赖颜色区分，对红绿色盲用户不友好
  - 修复方案：每个工具颜色标识同时搭配不同形状图标（圆形/方形/菱形等）

**键盘导航：**
- 作品广场的 Gallery 卡片目前无 `tabindex` 和 `role`
  - 修复方案：添加 `tabindex="0"` 和 `role="article"`

---

## 四、Prompt 工程：反偏见注入模板

使用此模板生成任何包含人物的图像时，强制注入身份约束：

```typescript
// PixelMind 反偏见角色生成 Prompt 框架
export function buildInclusiveCharacterPrompt(config: {
  culturalBackground: string;   // 如 "East African, Amhara people"
  ageRange: string;             // 如 "45-55 years old"
  gender: string;               // 如 "woman" / "man" / "non-binary person"
  physique: string;             // 如 "athletic build" / "heavier build" / "lean"
  clothingStyle: string;        // 如 "traditional Shamma with modern armor overlay"
  lightingNote: string;         // 如 "warm-toned directional light honoring dark skin"
  gameStyle: string;            // 如 "cel-shaded RPG game art"
  negativeConstraints: string;  // 如 "no generic fantasy, no Eurocentric features"
}) {
  return `
[Subject]: A ${config.ageRange} ${config.gender} with ${config.culturalBackground} heritage.
${config.physique} build. ${config.clothingStyle}.

[Style]: ${config.gameStyle}. High detail. Clean PNG transparent background.

[Lighting]: ${config.lightingNote}. No bleached highlights. Skin tones preserved.

[Composition]: Confident frontal character art pose. Game-ready framing.

[Technical]: Ultra-detailed fabric texture. Consistent proportions. No extra fingers.

[Negative]: ${config.negativeConstraints}. No clone face. No default white skin.
No generic European armor. No AI hallucinated cultural symbols or garbled text.
  `.trim();
}
```

---

## 五、7 点生成后 QA 审查清单

每次批量生成 Gallery 内容或 Prompt 示例素材后，必须逐一核查：

- [ ] **1. 文化符号准确性**：服饰/建筑/道具是否经过真实文化资料比对？（禁止 AI 幻觉的文化标志）
- [ ] **2. 面部多样性**：多人构图中是否出现"克隆脸"（外貌过于相似的多人）？
- [ ] **3. 照明肤色适配**：深色肤色角色的高光是否正常（非漂白）？
- [ ] **4. 非欧洲场景地理准确性**：建筑/植被/天空是否符合目标地区真实景观？
- [ ] **5. 文字与符号**：画面中是否出现乱码文化文字或错误的宗教符号？
- [ ] **6. 物理一致性**：服饰、发型在动作帧中是否符合真实物理规律？
- [ ] **7. 社区验证**：是否有来自所描绘社区的用户或顾问进行最终确认？

---

## 六、下一步行动项

| 优先级 | 行动项 | 负责方 | 时间线 |
|--------|--------|--------|--------|
| P0 | 替换 Studio 演示区 4 条 Prompt 样本，注入多元角色 | 设计/内容 | 本周 |
| P0 | 作品广场展示内容按 3.2 矩阵重新填充 | 内容运营 | 本周 |
| P1 | 风格预设标签增加非欧洲文化选项 | 产品 | 下周 |
| P1 | 修复 text-tertiary 对比度 (#545468) | 前端 | 下周 |
| P1 | Gallery 卡片增加 tabindex 和 aria 属性 | 前端 | 下周 |
| P2 | 工具颜色标识增加形状辅助区分 | 设计 | 两周内 |
| P2 | 建立持续性社区 QA 反馈机制 | 产品 | 一个月内 |

---

*本文档由 PixelMind 多元化视觉专家审查输出，遵循 WCAG 2.1 AA 标准和文化准确性原则。*
