# Skill: Balanced Academic Mind Map from Textbook Chapter

## When to use this skill

Use this skill when the user provides a textbook chapter, lecture note, handout, or academic PDF and asks for a mind map like the “balanced version”:

- The mind map should cover the chapter comprehensively but not excessively.
- It should be visually balanced, with branches distributed on both left and right sides.
- Node text should contain brief explanations and key formulas, not only topic names.
- Mathematical formulas must be properly rendered, not shown as raw LaTeX.
- Explicit `\n` strings must never appear in final visual output; all line breaks must be actual line breaks.
- No legend is needed unless the user explicitly asks for one.

The target output is a study-oriented mind map, suitable for review before exams.

## Core output standard

Generate at least one polished visual mind map and, when possible, also provide editable source files.

Preferred deliverables:

1. A single-page balanced mind map in PDF.
2. PNG preview.
3. SVG vector version.
4. Editable source file, preferably LaTeX/TikZ or another source format that preserves formulas.
5. Optional A4 split-print version if the map is too dense for one page.

The visual style should be close to:

- central title node placed near the horizontal center;
- major chapter sections distributed to left and right;
- each major branch uses a distinct but restrained color;
- leaf nodes are rounded rectangles;
- lines are clean and not overcrowded;
- formulas are rendered as math, not plain text;
- each node contains 1–3 short lines;
- no separate legend.

## Content selection principles

The mind map should be “complete but not too fine.”

For each section of the chapter:

1. Keep the main concept.
2. Include the essential definition or formula.
3. Include key theorem, criterion, or solving procedure.
4. Omit long proofs unless a proof method itself is a testable technique.
5. Omit repetitive examples unless an example introduces a standard algorithm.
6. Prefer formulas and operational rules over vague labels.

A good leaf node should look like this:

- Bad: `正交矩阵`
- Good:
  ```text
  正交矩阵
  P^T P=E ⇔ 列/行向量为标准正交组；
  P^{-1}=P^T，|P|=±1。
  ```

- Bad: `特征值`
- Good:
  ```text
  定义
  Ax=λx，x≠0；
  λ 为特征值，x 为对应特征向量。
  ```

For a math-heavy chapter, every major branch should contain at least some formulas, but not every formula in the source chapter needs to appear.

## Layout rules

Use a balanced two-sided layout.

1. Put the chapter title in the center.
2. Divide the major sections into two groups:
   - left side: approximately half of the sections;
   - right side: the remaining sections.
3. Balance by visual weight, not only by section count.
   - A section with many nodes counts as heavier.
   - If one side is too dense, move one medium-sized section to the other side.
4. Keep branch direction consistent:
   - left branches extend leftward;
   - right branches extend rightward.
5. Avoid placing all branches on the right side.
6. Avoid extremely long horizontal nodes; use actual line breaks.
7. Keep formulas compact and split long formulas across lines.

For a 7-section chapter, a good default distribution is:

- Left: §1, §2, §3, §4
- Right: §5, §6, §7

But adjust this if the chapter structure differs.

## Node density rules

For each major section, use about 4–7 leaf nodes.

Each leaf node should usually contain:

- line 1: short label;
- line 2: definition/formula/procedure;
- line 3: condition, consequence, or use case.

Avoid making a node exceed 4 lines. If a concept needs more, split it into two nodes.

Do not create tiny nodes that contain only a term unless the term is self-explanatory and paired with nearby detail.

## Formula rendering rules

Final visual output must render formulas correctly.

Do not leave raw LaTeX such as:

- `\lambda`
- `\sum`
- `x^T A x`
- `\sqrt{...}`

as visible source code unless the output format is a Markdown source file.

For PDF/SVG/PNG visual output:

- Use a math-capable renderer such as LaTeX/TikZ, Typst, MathJax-to-SVG, or another method that renders formulas.
- Use actual symbols where appropriate: `λ`, `Σ`, `√`, `≤`, `⇔`, `⇒`.
- Superscripts/subscripts should render properly.
- Matrix expressions should be readable.
- Long expressions should be shortened or broken across lines.

Acceptable visual text examples:

- `Ax=λx，x≠0`
- `|A−λE|=0`
- `P^TAP=diag(λ₁,…,λₙ)`
- `f=x^TAx`
- `x=Cy ⇒ f=y^T(C^TAC)y`
- `Δ₁>0，Δ₂>0，…，Δₙ>0`

## Line break rules

All intended line breaks must be actual line breaks.

Never show literal `\n` in any visual output.

Before finalizing, inspect the generated PDF/PNG/SVG and confirm:

- no node contains visible `\n`;
- all multiline labels are actually multiline;
- formulas are rendered or converted into readable mathematical symbols;
- no raw LaTeX command is visible in the visual output.

## Recommended production method

Prefer LaTeX/TikZ when mathematical rendering quality matters.

Recommended workflow:

1. Extract and organize chapter content.
2. Draft a hierarchical outline:
   - chapter title;
   - major sections;
   - leaf nodes with short explanations and formulas.
3. Decide left/right distribution.
4. Generate LaTeX/TikZ source.
5. Compile to PDF.
6. Convert PDF to PNG preview and/or SVG.
7. Inspect output visually.
8. Fix:
   - visible raw LaTeX;
   - visible `\n`;
   - overly long nodes;
   - one-sided imbalance;
   - overlapping branches;
   - unreadably small text.

If LaTeX compilation is unavailable, use SVG/HTML with MathJax, but still export a rendered version for the user.

## Suggested TikZ style

Use this kind of structure if creating a LaTeX/TikZ mind map:

```latex
\documentclass[tikz,border=8pt]{standalone}
\usepackage{ctex}
\usepackage{amsmath,amssymb}
\usetikzlibrary{mindmap,trees,shadows,positioning}

\begin{document}
\begin{tikzpicture}[
  font=\small,
  every node/.style={align=center},
  root/.style={
    rounded rectangle,
    draw,
    thick,
    fill=black!85,
    text=white,
    minimum width=3.6cm,
    minimum height=1.2cm
  },
  section/.style={
    rounded rectangle,
    draw,
    thick,
    fill=#1!18,
    text width=3.7cm,
    minimum height=0.9cm
  },
  leaf/.style={
    rounded rectangle,
    draw,
    fill=#1!7,
    text width=5.4cm,
    inner sep=4pt
  }
]
% central node, section nodes, and leaf nodes here
\end{tikzpicture}
\end{document}
```

Use real line breaks inside nodes, for example:

```latex
\node[leaf=blue] {
  \textbf{正交矩阵}\\
  $P^TP=E \Leftrightarrow$ 列/行向量为标准正交组；\\
  $P^{-1}=P^T,\ |P|=\pm1$。
};
```

Do not write:

```latex
\node {正交矩阵\n$P^TP=E$};
```

## Quality checklist

Before giving the final answer, verify all items below.

Content:

- The chapter’s major sections are all represented.
- Each major branch has definitions, formulas, or procedures, not just headings.
- Important theorems and criteria are included.
- The level of detail is suitable for review, not a full textbook rewrite.

Visual layout:

- Left and right branches are both present.
- The two sides are roughly balanced.
- No severe overlaps.
- No obvious one-sided crowding.
- Text size is readable.

Math and formatting:

- No literal `\n` appears.
- No raw LaTeX commands appear in visual outputs.
- Formulas are correctly rendered or converted into readable math symbols.
- Long formulas are split across lines.
- Chinese and math text display correctly.

Files:

- Provide PDF.
- Provide PNG preview.
- Provide SVG or another editable/vector version.
- Provide source file.
- If useful, provide a ZIP containing all outputs.

## Final response format

When returning files to the user, be concise.

Example:

“已按这个 skill 生成均衡版思维导图：左侧/右侧分支已重新平衡，节点内是真换行，公式已渲染，没有保留 raw LaTeX。”

Then provide links:

- PDF
- PNG
- SVG
- source file
- ZIP package

Do not add a legend unless requested.
Do not over-explain the production process unless the user asks.
