---
name: "iterative-evaluation"
description: "Designs weighted multi-dimensional evaluation systems and iteratively improves deliverables via sub-agent evaluation until target score is reached. Invoke when user wants quality assurance through structured scoring with iterative refinement, or asks to '设计评价体系'/'评判'/'迭代优化' with a score target."
---

# Iterative Evaluation

This skill captures a reusable workflow for **designing a weighted evaluation system, scoring a deliverable via an independent sub-agent, and iterating until the target score is reached**. It produces three artifacts: an evaluation scale, an improved deliverable, and an iteration report.

## When to Use

- User asks to "设计评价体系" / "设计评价量表" with a target score
- User wants an independent agent to judge a deliverable and iterate until passing
- User says "和之前一样，设计一个评价体系" or references prior evaluation workflows
- Any task requiring structured quality assurance with a numeric pass/fail threshold

## Workflow Overview

```
┌─────────────────────────────────────────────────┐
│  1. 设计评价量表 (Evaluation Scale)              │
│     · N 维加权体系 · 4 档评分 · 目标线           │
├─────────────────────────────────────────────────┤
│  2. 起草交付物 v1 (Draft Deliverable)            │
├─────────────────────────────────────────────────┤
│  3. 独立 Agent 评价 (Independent Evaluation)     │
│     · 逐维打分 · 汇总总分 · 判定是否达标         │
├─────────────────┬───────────────────────────────┤
│  4a. 未达标     │  4b. 已达标                    │
│  · 缺陷诊断     │  · 确认结论                    │
│  · 定向修复     │  · 生成迭代报告                │
│  · 回到 Step 3  │  · 结束                        │
└─────────────────┴───────────────────────────────┘
```

## Step-by-Step Instructions

### Step 1: Design the Evaluation Scale

Create a weighted multi-dimensional evaluation system as an HTML file.

**Design principles:**
1. **Dimension selection**: Choose 6-8 dimensions that cover the deliverable's quality comprehensively. Common dimensions:
   - 覆盖完整性 (Coverage) — does it cover all required topics
   - 资源质量 (Quality) — are sources authoritative and diverse
   - 画像匹配度 (Profile Fit) — does it match the target audience
   - 可执行性 (Actionability) — can the user act on it
   - 时效性 (Timeliness) — is the content current
   - 结构清晰度 (Structure) — is the layout clear and navigable
   - 可验证性 (Verifiability) — are claims backed by sources
   - 实用增值性 (Value-Add) — does it offer more than a basic listing

2. **Weight allocation**: Assign weights to each dimension summing to 100. Prioritize:
   - Core quality dimensions (coverage, quality) get 15-18 points
   - Execution dimensions (actionability, profile fit) get 10-15 points
   - Supporting dimensions (timeliness, structure) get 8-10 points
   - Enhancement dimensions (verifiability, value-add) get 6-8 points

3. **Four-tier scoring**: Each dimension uses 4 tiers:
   - 优 (Excellent) = 100% of weight
   - 良 (Good) = 75% of weight
   - 中 (Average) = 50% of weight
   - 差 (Poor) = 25% of weight

4. **Target line**: Set a target score (typically 90-95). Lower the bar by 2-4 points for deliverables with external uncertainty (e.g., resource timeliness).

5. **Output format**: HTML with:
   - Dimension table (number, name, weight, 4-tier criteria)
   - Target score callout
   - Scoring formula explanation
   - Same dark editorial design as other deliverables (see Design System below)

### Step 2: Draft the Deliverable (v1)

Create the initial version of the deliverable following the evaluation scale's criteria. Aim for quality but expect the first version to have gaps — the evaluation will surface them.

### Step 3: Launch Independent Evaluation Agent

Use the `Agent` tool with `subagent_type: "general_purpose_task"` to evaluate the deliverable. **The evaluation agent must be independent from the content creation process.**

**Critical: Provide the agent with:**
1. The full evaluation criteria (all dimensions, weights, tier definitions)
2. The complete content of the deliverable being evaluated
3. Any contextual information needed (e.g., user profile, project background)
4. A structured output format requirement

**Agent prompt template:**

```
你是一个独立的质量评价代理。请严格按照以下 N 维加权评价量表，对 [deliverable name] 进行逐维打分。

## N 维评价标准（总分100）

[Insert all dimensions with weights and 4-tier criteria]

## 上下文信息

[Insert user profile, project background, or other context needed for evaluation]

## 待评价的交付物内容

[Insert full content of the deliverable being evaluated]

## 你的任务

请严格按以下格式输出评价报告：

### 逐维评分
对每个维度：
1. 给出档位（优/良/中/差）
2. 给出该维度得分（权重 × 档位比例）
3. 给出评分理由（2-3句具体依据）

### 总分计算
列出 N 维得分加总，判断是否≥[target]。

### 如未达标
列出最薄弱的 2-3 个维度，给出具体可操作的改进建议。

### 如已达标
给出最终结论：确认达标，列出该版本的优势亮点。

请务必客观、严格、有据可依地评价。不要因为"已经迭代到vN"就放松标准。
```

### Step 4a: If Below Target — Diagnose and Fix

1. **Identify defects**: From the agent's feedback, extract the weakest dimensions and their specific issues.
2. **Design fixes**: For each defect, design a targeted fix that directly addresses the scoring gap.
3. **Apply fixes**: Modify the deliverable, incrementing the version number (v2, v3, ...).
4. **Re-evaluate**: Return to Step 3 with the new version.

**Common fixes by dimension:**

| Dimension | Common Defect | Fix Pattern |
|-----------|---------------|-------------|
| 可验证性 | No URLs, no source labels | Add clickable URLs for every resource; add confidence labels ([官方]/[经典]/[社区]); add cross-validation for core items |
| 资源质量 | Too few resources, weak sources | Ensure ≥3 resource types per item; add official docs and classic textbooks; add alternatives |
| 时效性 | No version dates | Add version numbers and update dates for all resources; mark maintenance status |
| 结构清晰度 | No visual hierarchy | Add dependency diagrams, navigation anchors, legend bars, visual grouping |
| 画像匹配 | Generic recommendations | Add difficulty tiers, prerequisite chains, milestone-based progression |
| 可执行性 | Missing action items | Add learning paths, time estimates, acceptance criteria, project suggestions |

### Step 4b: If Target Reached — Generate Iteration Report

Create an HTML report documenting the full evaluation journey:

**Required sections:**
1. **评价方法** — The N-dimensional system with weight distribution
2. **v1 评价** — Initial score table + progress bar showing gap to target
3. **问题诊断** — Each defect with severity, impact, and fix direction
4. **v2 改进** — Each improvement with what changed and which dimension it targets
5. **v2 评分** — Final score table with v1→v2 comparison + delta column
6. **达标结论** — Final verdict, advantage highlights, optional v3 suggestions

**Visual elements:**
- Score badge (pass=green, fail=red)
- Progress bars for each version with target line marker
- Dimension bar charts comparing v1 vs v2
- Delta (Δ) column showing point changes

## Design System

All HTML deliverables use a consistent dark editorial design:

### CSS Variables
```css
:root{
  --bg:#0c0b10; --bg2:#15141b; --bg3:#1d1b25;
  --ink:#eae6e0; --muted:#8a8595; --rule:#2a2735;
  --accent:#d4a574; --accent2:#6cae75;
  --warn:#e8956b; --bad:#c85a5a; --good:#6cae75;
}
```

### Typography
- **Headings**: Fraunces (serif, 600-800 weight)
- **Body**: Manrope (400-700 weight)
- **Data/Code**: JetBrains Mono

### Key Components
- `.topline` — fixed 1px top border with 80px accent segment
- `.hero` — eyebrow (mono uppercase) + large serif title + muted subtitle
- `section` — card with 1px border, 12px radius, fade-up animation
- `nav.toc` — sticky pill navigation
- `.s-table` — score table with tier colors (green/accent/warn/bad)
- `.pbar` — animated progress bar with target line marker
- `.issue` — red left-border callout (defects)
- `.imprv` — green left-border callout (improvements)
- `.highlight` — accent left-border callout (key findings)

### Google Fonts Link
```html
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,400;0,9..144,600;0,9..144,800;1,9..144,400;1,9..144,600&family=Manrope:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
```

## Quality Rules

1. **Evaluation independence**: The evaluation agent must not be the same context that created the deliverable. Always use `Agent` tool with `subagent_type: "general_purpose_task"` for fresh evaluation.
2. **Strict scoring**: Do not relax standards for later versions. If anything, later versions should be scored more strictly (e.g., "画像匹配度" dropped from 优 to 良 in v2 because the evaluator applied stricter scrutiny).
3. **Full content to agent**: The evaluation agent has no conversation history. Provide the COMPLETE deliverable content and ALL evaluation criteria in the agent prompt.
4. **No partial evaluation**: Every dimension must be scored. No dimension can be skipped or marked "N/A".
5. **Actionable feedback**: Every "良" or lower rating must include a specific, actionable improvement suggestion.
6. **Version tracking**: Maintain clear version numbering (v1, v2, v3...) and document all changes between versions.
7. **Cleanup**: Delete any temporary files created by evaluation agents (e.g., `.md` files the agent might create). Only keep the final HTML deliverables.

## File Organization

After all iterations, organize deliverables into a clean structure:

```
[project folder]/
├── [evaluation scale].html      ← The N-dimensional scoring system
├── [deliverable].html            ← The final version (v2/v3/...)
├── [iteration report].html       ← v1→v2 evaluation journey
└── (optional) [gap analysis].html ← If the deliverable expanded scope from a prior version
```

Cross-references between files use relative paths. When files are in the same directory, use `href="filename.html"`. When referencing files in a parent/sibling directory, use `href="../folder/filename.html"`.

## Checklist

Before declaring the task complete, verify:
- [ ] Evaluation scale HTML created with all dimensions, weights, and 4-tier criteria
- [ ] Deliverable drafted and evaluated by an independent agent
- [ ] If below target: defects diagnosed, fixes applied, re-evaluated
- [ ] Final score ≥ target
- [ ] Iteration report HTML created with v1→v2 comparison
- [ ] All HTML files use the dark editorial design system
- [ ] Cross-references between files use correct relative paths
- [ ] Temporary files (e.g., .md files from evaluation agent) cleaned up
