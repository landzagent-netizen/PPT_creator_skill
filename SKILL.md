---
name: ppt-creator
description: >
  从原稿讲稿生成乔布斯风格口播稿和 PPT 的完整工作流。使用场景：用户提供演讲稿、视频脚本、播客稿、大纲等原始文稿，需要生成可直接使用的口播稿和配套 PPT。
  当用户提到"做PPT"、"生成演示文稿"、"创建PPT"、"讲稿优化"、"视频脚本"、"抽卡"，或提供原稿/大纲文件要求生成演示内容时，使用此 skill。
  即使用户只说"帮我优化一下这个稿子"或"把这个内容做成演示"，也应触发此 skill。
  支持多版本混选：生成多套 PPT 后，用户可以从每套中选择喜欢的页面，合并成最终的 presentation_final.html。
---

# PPT Creator

从用户提供的原稿出发，生成两个产物：
1. **修正版口播稿**（`script.md`）— 完整逐字稿，可直接对着讲
2. **PPT**（`presentation.html`）— 乔布斯式标题 + 视觉化标注，Slidev 格式

口播稿是第一优先级产物，PPT 是口播稿的视觉配套。

## 核心原则

- **保留原意**：不改变作者的核心观点和立场
- **增强表达**：用乔布斯风格让内容更有感染力
- **视觉化**：每个关键点都有对应的 PPT 画面标注

## 使用场景

| 场景 | 风格 | 气质 |
|------|------|------|
| launch（科技产品发布） | 简洁震撼，类似苹果发布会 | 深色背景、大字居中、留白多 |
| edu（知识分享/教学） | 逻辑清晰，易于理解 | 浅色背景、结构化布局 |

AI 根据内容自动判断场景类型，用户可通过 `--style=launch` / `--style=edu` 覆盖。

## 技术栈

- Slidev（Vue 3 + Markdown）
- Vite 热更新预览
- 输出为 HTML 文件

---

## 工作流程（8 步）

### Step 1：读取与解析原稿

**输入**：用户提供的原稿文件（Markdown / 纯文本 / 文件路径）

- 读取原稿全文
- 识别原稿类型（视频脚本 / 播客稿 / 演讲稿 / 大纲）
- 拆分结构：识别章节边界、段落、要点
- 统计：总字数、章节数、预估演讲时长

**输出**：结构化原稿对象

### Step 2：场景判断

**输入**：Step 1 的结构化原稿

- AI 分析内容，判断场景类型（launch / edu）
- 向用户展示判断结果，用户可通过 `--style` 覆盖

**输出**：确定的场景类型

### Step 3：生成口播稿（×N 并行）

**输入**：结构化原稿 + 场景类型

这是核心步骤。生成完整的逐字口播稿，不是摘要或要点。

读取 `references/script-rules.md`，按以下顺序改写：
1. 结构修正（开场→核心章节→收尾）
2. 开场五秒原则（钩子开场）
3. 语言风格修正（口语化、断句、互动）
4. 场景化表达（抽象配具体）
5. 冲突式表达（认知冲突、对比放大）
6. 收尾（回扣钩子、行动号召）
7. PPT 画面标注（`<!-- slide: 画面描述 -->`）

**输出**：`script.md`（单版本）或 `script_1.md` ~ `script_N.md`（多版本）

### Step 4：用户选定口播稿

- 展示各版本摘要（开场钩子 + 章节标题 + 收尾方式）
- 用户选定一个版本，不满意可要求重新生成

**输出**：`script_final.md`

### Step 5：生成乔布斯式标题

**输入**：最终口播稿（含画面标注）

读取 `references/title-rules.md`，执行标题生成四步法：
1. 提取核心概念（每章一个）
2. 寻找具象化比喻
3. 压缩至 ≤ 12 字
4. 质量检查

同时确定每页的颜色语义方案。

**输出**：每个章节的标题 + 视觉内容方案

### Step 6：生成 PPT（×N 并行）

**输入**：标题和视觉方案 + 最终口播稿

**必须严格遵循 `references/visual-spec.md` 规范**，生成乔布斯风格 PPT：

#### CSS 规范（必须包含）

根据分配的 flavor 选择对应的 CSS 样式。**整套 PPT 必须使用同一种 flavor**。

> **推荐风格**：以下 5 种 flavor 源自用户验证过的高质量 PPT，优先使用这些定义。

```css
/* ========== 通用基础样式（所有 flavor 必须包含）========== */
* { margin: 0; padding: 0; box-sizing: border-box; }
html, body {
  font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Helvetica Neue", Arial, sans-serif;
  overflow: hidden;
  width: 100%; height: 100%;
}
.slide {
  display: none; flex-direction: column; align-items: center; justify-content: center;
  width: 100%; height: 100%; padding: 60px; text-align: center;
}
.slide.active { display: flex; }
```

---

**Flavor 1：P1经典**（权重最高）
```css
/* P1经典风格：金色渐变 + 大量留白 + 视觉标注 */
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@300;400;500;700;900&display=swap');

html, body { font-family: 'Noto Sans SC', -apple-system, BlinkMacSystemFont, sans-serif; }
body { background: #0a0a0a; color: #ffffff; overflow: hidden; height: 100vh; width: 100vw; }
.slide { width: 100%; height: 100%; display: none; flex-direction: column; justify-content: center; align-items: center; padding: 80px; position: relative; background: #0a0a0a; }
.slide.active { display: flex; }
.slide-title { font-size: 4.5rem; font-weight: 900; color: #ffffff; text-align: center; letter-spacing: -0.02em; line-height: 1.1; margin-bottom: 40px; }
.slide-title.gold { background: linear-gradient(135deg, #ffd700 0%, #ffaa00 50%, #ff8800 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; }
.slide-subtitle { font-size: 1.8rem; font-weight: 300; color: rgba(255, 255, 255, 0.7); text-align: center; max-width: 800px; line-height: 1.6; }
.slide-content { font-size: 1.4rem; font-weight: 400; color: rgba(255, 255, 255, 0.6); text-align: center; max-width: 900px; line-height: 1.8; margin-top: 30px; }
.slide-visual { font-size: 1.2rem; color: rgba(255, 255, 255, 0.4); margin-top: 60px; font-style: italic; }
.slide-cover { background: linear-gradient(180deg, #0a0a0a 0%, #1a1a1a 100%); }
.slide-cover .main-title { font-size: 6rem; font-weight: 900; letter-spacing: -2px; margin-bottom: 40px; color: #ffffff; }
.slide-cover .sub-title { font-size: 1.8rem; font-weight: 300; color: rgba(255, 255, 255, 0.6); }
.slide-number { position: absolute; bottom: 40px; right: 60px; font-size: 14px; color: #666; font-family: "SF Mono", monospace; }
```

---

**Flavor 2：P2简洁**（权重次高）
```css
/* P2简洁风格：白到金渐变 + 极简排版 */
body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif; background: #0a0a0a; color: #ffffff; overflow: hidden; }
.slide { width: 100vw; height: 100vh; display: none; flex-direction: column; justify-content: center; align-items: center; padding: 80px; position: relative; }
.slide.active { display: flex; }
.slide-number { position: absolute; bottom: 40px; right: 60px; font-size: 14px; color: #666; font-family: "SF Mono", monospace; }
.cover h1 { font-size: 72px; font-weight: 600; letter-spacing: -2px; margin-bottom: 24px; background: linear-gradient(135deg, #ffffff 0%, #ffd700 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; }
.cover .subtitle { font-size: 28px; color: #888; font-weight: 300; letter-spacing: 8px; }
.cover .tagline { margin-top: 60px; font-size: 20px; color: #555; letter-spacing: 4px; }
.content h2 { font-size: 56px; font-weight: 600; letter-spacing: -1px; margin-bottom: 48px; line-height: 1.2; }
.content h2 .gold { color: #ffd700; }
.content .body-text { font-size: 24px; color: #888; font-weight: 300; line-height: 1.8; max-width: 800px; }
.content .highlight { color: #fff; font-weight: 400; }
.big-text h2 { font-size: 64px; font-weight: 700; letter-spacing: -2px; }
```

---

**Flavor 3：P3暖金**（权重第三）
```css
/* P3暖金风格：白+金渐变 + 深空灰背景 */
body { background: #0a0a0a; color: #ffffff; overflow: hidden; }
.slide { display: none; flex-direction: column; align-items: center; justify-content: center; width: 100%; height: 100%; padding: 60px; text-align: center; }
.slide.active { display: flex; }
.slide-cover { background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%); }
.slide-cover .main-title { font-size: 72px; font-weight: 700; letter-spacing: -2px; margin-bottom: 40px; background: linear-gradient(90deg, #ffffff, #ffd700); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; }
.slide-cover .sub-title { font-size: 28px; color: #888; font-weight: 300; }
.slide-content { background: #0a0a0a; }
.slide-content .slide-title { font-size: 56px; font-weight: 700; margin-bottom: 60px; color: #ffffff; max-width: 80%; }
.slide-content .slide-desc { font-size: 24px; color: #666; font-weight: 300; max-width: 60%; line-height: 1.6; }
.slide-end { background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%); }
.slide-end .end-title { font-size: 64px; font-weight: 700; color: #ffd700; }
.slide-number { position: fixed; bottom: 30px; right: 40px; font-size: 14px; color: #666; font-weight: 300; }
```

---

**Flavor 4：P4光晕**（权重第四）
```css
/* P4光晕风格：金色光晕 + 大字居中 */
body { font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "Helvetica Neue", Arial, sans-serif; background: #0a0a0a; color: #ffffff; overflow: hidden; }
.slide { width: 100vw; height: 100vh; display: flex; flex-direction: column; justify-content: center; align-items: center; padding: 60px; position: relative; background: #0a0a0a; }
.slide::before { content: ''; position: absolute; top: 0; left: 0; right: 0; bottom: 0; background: radial-gradient(ellipse at center, rgba(255,255,255,0.03) 0%, transparent 70%); pointer-events: none; }
.slide.cover { background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%); }
.slide.cover::after { content: ''; position: absolute; bottom: -200px; right: -200px; width: 600px; height: 600px; background: radial-gradient(circle, rgba(255,215,0,0.15) 0%, transparent 60%); pointer-events: none; }
.cover .main-title { font-size: 6rem; font-weight: 700; letter-spacing: -2px; text-align: center; margin-bottom: 30px; background: linear-gradient(135deg, #ffffff 0%, #ffd700 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; }
.cover .subtitle { font-size: 1.8rem; color: rgba(255,255,255,0.6); font-weight: 300; letter-spacing: 4px; }
.slide.content { padding: 80px 120px; }
.slide.content .slide-number { position: absolute; bottom: 40px; left: 60px; font-size: 0.9rem; color: rgba(255,255,255,0.3); font-family: "SF Mono", monospace; }
.slide.content .slide-title { font-size: 4.5rem; font-weight: 700; text-align: center; margin-bottom: 50px; line-height: 1.2; letter-spacing: -1px; }
```

---

**Flavor 5：P5双色调**（权重第五）
```css
/* P5双色调风格：白色系 + 金蓝双色调 + 顶部页码 */
:root { --bg-primary: #0a0a0a; --bg-secondary: #111111; --text-primary: #ffffff; --text-secondary: #888888; --accent-gold: #d4a853; --accent-blue: #4a90d9; }
body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif; background: var(--bg-primary); color: var(--text-primary); overflow: hidden; }
.slide { width: 100vw; height: 100vh; display: flex; flex-direction: column; justify-content: center; align-items: center; padding: 80px; position: relative; background: var(--bg-primary); }
.slide::before { content: ''; position: absolute; top: 0; left: 0; right: 0; bottom: 0; background: radial-gradient(ellipse at center, transparent 0%, var(--bg-primary) 70%); pointer-events: none; }
.slide-cover { background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 50%, #0a0a0a 100%); }
.slide-cover .title { font-size: 4.5rem; font-weight: 700; letter-spacing: -2px; text-align: center; background: linear-gradient(180deg, #ffffff 0%, #cccccc 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; margin-bottom: 40px; }
.slide-cover .subtitle { font-size: 1.8rem; color: var(--text-secondary); font-weight: 300; letter-spacing: 8px; text-transform: uppercase; }
.slide-cover .tagline { position: absolute; bottom: 120px; font-size: 1.2rem; color: var(--accent-gold); letter-spacing: 4px; }
.slide-content { background: var(--bg-primary); }
.slide-content .slide-number { position: absolute; top: 60px; right: 80px; font-size: 0.9rem; color: var(--text-secondary); opacity: 0.5; }
.slide-content .main-title { font-size: 4rem; font-weight: 700; text-align: center; margin-bottom: 40px; line-height: 1.3; }
```

#### 设计要求
- **必须使用分配的 flavor 样式**，整套风格一致
- 乔布斯式标题 ≤12 字，居中大字，60%以上留白
- 每页只展示 1 个核心概念
- 必须包含 slide-number 页面指示器
- 必须支持键盘（←→/空格）和鼠标（点击左/右半屏）导航

**输出**：`presentation.html`（单版本）或 `presentation_1.html` ~ `presentation_N.html` + `index.html`（多版本）

### Step 7：用户选定 PPT（或混选页面）

用户有两种选择方式：

**方式 A：选整套**
- 通过 `index.html` 预览，选定一套完整的 PPT

**方式 B：混选页面**（新增）
- 用户说"我想混选"、"从不同版本选页面"、"拼一套"
- 展示所有版本的页面列表（每套 PPT 的第X页标题）
- 用户指定从哪些版本选哪些页面，例如：
  - "v1 的封面、v2 的第3页、v3 的第5-7页、v4 的第2页"
  - "只要 v1 的前5页 + v3 的结尾页"
- AI 根据用户选择，从对应 HTML 文件中提取 `<slide>` 片段，合并生成 `presentation_final.html`

**输出**：
- 方式 A：`presentation_X.html` → `presentation_final.html`
- 方式 B：`presentation_final.html`（合并后的 PPT）

### Step 8：自动预览

- 安装依赖（如未安装）
- 启动 Vite 开发服务器
- `open http://localhost:3030` 自动打开浏览器

---

## 抽卡模式

默认生成 1 个版本。用户可通过 `--copies N` 生成 N 个版本。

当 N > 1 时：
- **口播稿阶段**：派发 N 个 subagent 并行，每个生成 `script_x.md`
- **PPT 阶段**：派发 N 个 subagent 并行，每个读取 `script_x.md` 生成 `presentation_x.html`，另生成 `index.html` 索引页
- **风格分配（等权重循环）**：5 种风格按顺序循环分配，确保版本间差异最大化：
  - 第1套用 **P1经典**（金色渐变 + 大量留白）
  - 第2套用 **P2简洁**（白到金渐变 + 极简排版）
  - 第3套用 **P3暖金**（白+金渐变 + 深空灰背景）
  - 第4套用 **P4光晕**（金色光晕 + 大字居中）
  - 第5套用 **P5双色调**（白色系 + 金蓝双色调）
  - 第6套起循环：P1经典 → P2简洁 → P3暖金 → P4光晕 → P5双色调 → P1经典 → ...

一一对应关系：
```
script_1.md  →  presentation_1.html
script_2.md  →  presentation_2.html
script_3.md  →  presentation_3.html
```

用户从 N 套完整的「口播稿 + PPT」组合中选择一套。

## 文件输出目录

所有产物必须输出到以下目录结构：

```
/Users/mac_mini/Documents/ppt-generator/ppt-creator-workspace/
└── [PPT主题名称]/
    ├── script.md          # 修正版口播稿（单版本）
    ├── script_1.md ~ script_N.md   # 多版本口播稿
    ├── script_final.md    # 用户最终选定的口播稿
    ├── presentation.html  # PPT（单版本）
    └── presentation_1.html ~ presentation_N.html  # 多版本 PPT
```

**文件夹命名规则**：
- 文件夹名称根据 PPT 主题命名，使用简洁的英文或拼音
- 避免特殊字符，词之间用下划线 `_` 连接
- 示例：`AI产品发布会`、`新能源汽车发布会`

**版本命名规则**（重要）：
- 每次生成都会创建新文件夹，不会覆盖原文件
- 文件夹命名格式：`[主题名称]_[YYYYMMDD_HHMMSS]`
- 示例：`PPT_Creator_Introduction_20260321_143052`
- index.html 也会在对应文件夹中生成
- 每次抽卡生成新文件夹，保留历史版本

## 文件命名

| 副本数量 | 口播稿 | PPT |
|---------|--------|-----|
| 1（默认）| `script.md` | `presentation.html` |
| N > 1 | `script_1.md` ~ `script_N.md` | `presentation_1.html` ~ `presentation_N.html` + `index.html` |

最终选定的口播稿保存为 `script_final.md`。

---

## 混选页面合并（技术实现）

当用户选择**方式 B：混选页面**时，按以下步骤合并 HTML：

### 1. 解析各版本 PPT 结构

读取 `presentation_X.html`，找到每个 `<slide>` 或 `<div class="slide">` 片段。

### 2. 提取目标页面

根据用户选择（如 "v1 第1页、v2 第3页、v3 第5-7页"），从对应 HTML 文件中提取 `<slide>` 片段。

### 3. 合并生成新 HTML

保持统一的 `<head>` 样式（深色背景、字体、全局 CSS），将提取的 `<slide>` 按顺序拼接。

### 4. 生成 presentation_final.html

输出到工作目录，文件名固定为 `presentation_final.html`。

### 合并示例

用户选择：`v1的第1页 + v2的第3页 + v3的第5-7页`

```html
<!-- 从 presentation_1.html 提取第1页 -->
<div class="slide">...</div>
<!-- 从 presentation_2.html 提取第3页 -->
<div class="slide">...</div>
<!-- 从 presentation_3.html 提取第5-7页 -->
<div class="slide">...</div>
<div class="slide">...</div>
<div class="slide">...</div>
```

### 注意事项

- 保持所有 `<slide>` 使用统一的全局 CSS（背景色 #0a0a0a、标题金色等）
- 确保导航功能（键盘/鼠标）在合并后仍可用
- 如果用户只选了某个版本的连续页面，可以直接复制该 HTML 的 `<head>` 部分
