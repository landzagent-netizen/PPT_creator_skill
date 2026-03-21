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
```css
/* 封面页 */
.slide-cover {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
}
.slide-cover .main-title {
  font-size: 72px;
  font-weight: 700;
  letter-spacing: -2px;
  margin-bottom: 40px;
  background: linear-gradient(90deg, #ffffff, #ffd700);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.slide-cover .sub-title {
  font-size: 28px;
  color: #888;
  font-weight: 300;
}

/* 内容页 */
.slide-content {
  background: #0a0a0a;
}
.slide-content .slide-title {
  font-size: 56px;
  font-weight: 700;
  margin-bottom: 60px;
  color: #ffffff;
  max-width: 80%;
}
.slide-content .slide-desc {
  font-size: 24px;
  color: #666;
  font-weight: 300;
  max-width: 60%;
  line-height: 1.6;
}

/* 冲击页 */
.slide-impact {
  background: #0a0a0a;
}
.slide-impact .impact-title {
  font-size: 64px;
  font-weight: 700;
  color: #ffd700;
}

/* 结尾页 */
.slide-end {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
}
.slide-end .end-title {
  font-size: 64px;
  font-weight: 700;
  color: #ffd700;
}

/* 页面指示器 */
.slide-number {
  position: absolute;
  bottom: 30px;
  right: 40px;
  font-size: 14px;
  color: #666;
  font-weight: 300;
}

/* 金色强调 */
.gold-text {
  color: #d4af37;
}
```

#### 设计要求
- 深色背景 `#0a0a0a`，金色强调 `#d4af37` 或 `#ffd700`
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
