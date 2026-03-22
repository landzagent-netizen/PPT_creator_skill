🇨🇳 中文 | 🇬🇧 [English](./README_en.md)

# PPT生成助手

将任意原稿（演讲稿、视频脚本、播客稿、大纲）一键转化为乔布斯风格口播稿 + PPT 的 Claude Code Skill。

口播稿是第一优先级产物，PPT 是口播稿的视觉配套。

## Features

- **完整逐字稿**：从原稿生成可直接对着讲的修正版口播稿，而非摘要或要点罗列
- **乔布斯风格标题**：每页一个核心概念，具象化、对比鲜明、一口气能说完
- **双场景适配**：自动判断 launch（产品发布）或 edu（知识分享）场景
- **抽卡模式**：一次生成 N 个差异化版本，用户选择最喜欢的一套
- **PPT 画面标注**：口播稿中内嵌 `<!-- slide: 画面描述 -->` 标注，确保图文对应
- **颜色语义系统**：工具/痛点/方案/数据等概念类型各有专属配色方案

## 快速开始

### 前提条件

- [Claude Code](https://docs.anthropic.com/claude-code) 已安装
- Node.js 18+（用于 Slidev PPT 预览）

### 安装

将本项目克隆到 Claude Code 的 skills 目录即可：

```bash
git clone https://github.com/your-username/ppt-creator.git ~/.claude/skills/ppt-creator
```

### 使用方式

在 Claude Code 对话框中触发，例如：

```
帮我把 /path/to/你的原稿.md 做成 PPT
```

或指定版本数量：

```
帮我把这个视频脚本做成 3 个版本的 PPT --copies 3
```

## 工作流程（8 步）

```
Step 1  →  读取与解析原稿
Step 2  →  场景判断（launch / edu）
Step 3  →  生成口播稿（并行 N 个版本）
Step 4  →  用户选定口播稿版本
Step 5  →  生成乔布斯式标题
Step 6  →  生成 PPT（Slidev HTML）
Step 7  →  用户选定 PPT 版本
Step 8  →  自动预览（Vite 热更新）
```

## 场景风格

| 场景 | 风格 | 视觉 |
|------|------|------|
| `launch`（产品发布） | 简洁震撼，类似苹果发布会 | 深色背景、大字居中、留白多 |
| `edu`（知识分享） | 逻辑清晰，易于理解 | 浅色背景、结构化布局 |

AI 自动判断场景类型，可通过 `--style=launch` / `--style=edu` 手动覆盖。

## 抽卡模式

默认生成 1 个版本。使用 `--copies N` 可一次生成 N 个差异化版本：

```
/ppt-creator --copies 3
```

输出结构：

```
script_1.md      →  presentation_1.html
script_2.md      →  presentation_2.html
script_3.md      →  presentation_3.html
                +  index.html（版本索引页）
```

每个版本在视觉编排、配色、布局风格上做差异化处理。

## 输出文件

| 数量 | 口播稿 | PPT |
|------|--------|-----|
| 1（默认）| `script.md` | `presentation.html` |
| N > 1 | `script_1.md` ~ `script_N.md` | `presentation_1.html` ~ `presentation_N.html` + `index.html` |

最终选定的口播稿保存为 `script_final.md`。

## 项目结构

```
ppt-creator/
├── SKILL.md                        # Skill 主定义（工作流说明）
├── README.md                       # 本文件
├── references/
│   ├── script-rules.md             # 口播稿修正规则（钩子、口语化、冲突式表达等）
│   ├── title-rules.md              # 乔布斯式标题生成规则 + 颜色语义系统
│   └── visual-spec.md              # PPT 视觉规范（Slidev 技术栈、着色规则）
└── evals/
    └── evals.json                  # 评测用例
```

## 核心规则

### 口播稿修正原则

- **开场五秒原则**：第一句必须有钩子（悬念/痛点/故事/数据/承诺）
- **口语化**：用"你/咱们"代替"用户/我们"，删书面语
- **场景化**：每个抽象概念配一个日常生活场景
- **冲突式表达**："大家都以为 X，但其实 Y"
- **收尾闭环**：回扣开场钩子，以行动号召结束

### 标题生成原则

- ≤ 12 字中文 / ≤ 8 词英文，一口气能说完
- 类型：重新定义型、数字冲击型、极简宣言型、问题型、对比颠覆型、比喻型、痛点直击型
- 质量检查：画面感、上下文独立性、非术语

### 颜色语义

| 概念类型 | 颜色 |
|---------|------|
| 工具/技术 | Cyan / Blue / Purple |
| 痛点/问题 | Red / Pink |
| 解决方案 | Green / Teal |
| 数据/指标 | Orange / Amber |
| 行动号召 | 品牌主色 / 高饱和强调色 |

## 技术栈

- **Slidev**（Vue 3 + Markdown）
- **Vite** 热更新预览
- 输出为独立 HTML 文件，无需额外依赖即可展示

## License

MIT
