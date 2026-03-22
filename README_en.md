# Title

🇬🇧 English | 🇨🇳 [中文](./README.md)

# PPT-Creator skill

A Claude Code Skill that transforms any raw manuscript — presentation draft, video script, podcast transcript, or outline — into a Jobs-style speaking script and PPT with a single command.

The speaking script is the primary deliverable; PPT is its visual companion.

## Features

- **Full Word-for-Word Script**: Generates a polished speaking script ready to present from, not a summary or bullet-point list
- **Jobs-Style Slide Titles**: One core concept per slide — vivid, contrasting, deliverable in a single breath
- **Dual Scenario Support**: Auto-detects `launch` (product announcement) or `edu` (knowledge sharing) scenarios
- **Multi-Version Mode**: Generate N differentiated versions at once, pick the best one
- **Slide Annotations**: Inline `<!-- slide: visual description -->` markers in the script ensure text and visuals stay in sync
- **Color Semantic System**: Each concept type (tool/pain point/solution/data) has its own dedicated color palette

## Quick Start

### Prerequisites

- [Claude Code](https://docs.anthropic.com/claude-code) installed
- Node.js 18+ (for Slidev PPT preview)

### Installation

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/your-username/ppt-creator.git ~/.claude/skills/ppt-creator
```

### Usage

Trigger directly in Claude Code, for example:

```
Turn /path/to/your/draft.md into a PPT
```

Or specify the number of versions:

```
Make 3 versions of this video script into PPT --copies 3
```

## Workflow (8 Steps)

```
Step 1  →  Read and parse the raw manuscript
Step 2  →  Determine scenario (launch / edu)
Step 3  →  Generate speaking script(s) (N versions in parallel)
Step 4  →  User selects a script version
Step 5  →  Generate Jobs-style slide titles
Step 6  →  Generate PPT (Slidev HTML)
Step 7  →  User selects a PPT version
Step 8  →  Auto-preview (Vite hot-reload)
```

## Scenario Styles

| Scenario | Style | Visuals |
|----------|-------|---------|
| `launch` (product announcement) | Clean and impactful, Apple keynote vibe | Dark background, large centered text, generous whitespace |
| `edu` (knowledge sharing) | Clear logic, easy to follow | Light background, structured layout |

AI auto-detects the scenario; override manually with `--style=launch` or `--style=edu`.

## Multi-Version Mode

Generates 1 version by default. Use `--copies N` to produce N differentiated versions:

```bash
/ppt-creator --copies 3
```

Output structure:

```
script_1.md      →  presentation_1.html
script_2.md      →  presentation_2.html
script_3.md      →  presentation_3.html
                +  index.html (version index)
```

Each version is differentiated in visual composition, color scheme, and layout style.

## Output Files

| Copies | Speaking Script | PPT |
|--------|----------------|-----|
| 1 (default) | `script.md` | `presentation.html` |
| N > 1 | `script_1.md` ~ `script_N.md` | `presentation_1.html` ~ `presentation_N.html` + `index.html` |

The final selected script is saved as `script_final.md`.

## Project Structure

```
ppt-creator/
├── SKILL.md                        # Skill definition (workflow reference)
├── README.md                       # This file (Chinese)
├── README_en.md                    # English version
├── references/
│   ├── script-rules.md             # Speaking script correction rules (hooks, colloquialization, conflict phrasing, etc.)
│   ├── title-rules.md              # Jobs-style title generation rules + color semantic system
│   └── visual-spec.md              # PPT visual spec (Slidev stack, color rules)
└── evals/
    └── evals.json                  # Evaluation test cases
```

## Core Rules

### Speaking Script Correction Principles

- **5-Second Opening Rule**: The very first sentence must have a hook (suspense / pain point / story / data / promise)
- **Colloquialization**: Use "you / we" instead of "users / we", strip formal language
- **Scene-Based Expression**: Pair every abstract concept with a real-life scenario
- **Conflict-Driven Writing**: "Everyone thinks X, but actually Y"
- **Closing Loop**: Tie back to the opening hook, end with a call to action

### Title Generation Principles

- ≤ 12 Chinese characters / ≤ 8 English words — deliverable in a single breath
- Types: redefinition, numeric impact, minimalist declaration, question, contrast subversion, metaphor, pain-point direct
- Quality checks: visual imagery, standalone clarity, no jargon

### Color Semantics

| Concept Type | Color |
|-------------|-------|
| Tool / Tech | Cyan / Blue / Purple |
| Pain Point / Problem | Red / Pink |
| Solution | Green / Teal |
| Data / Metric | Orange / Amber |
| Call to Action | Brand primary / high-saturation accent |

## Tech Stack

- **Slidev** (Vue 3 + Markdown)
- **Vite** hot-reload preview
- Outputs standalone HTML — no extra dependencies needed to present

## License

MIT
