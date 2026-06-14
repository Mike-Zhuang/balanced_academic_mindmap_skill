# Skill: Balanced Academic Mind Map with Collision-Safe Layout

## Use case

Use this skill when the user provides a textbook chapter, lecture note, handout, or academic PDF and wants a balanced study mind map like the previous “均衡版思维导图”.

The output should be a review-oriented academic mind map:

- Coverage is broad but not excessively detailed.
- Major branches appear on both the left and right sides.
- Nodes contain concise explanations and key formulas, not just labels.
- Mathematical formulas are rendered correctly in the final PDF/SVG/PNG.
- Literal `\n` strings must never appear; all intended line breaks must be actual line breaks.
- No legend is needed unless the user explicitly asks for one.
- Subtopics must not overlap or touch each other.

## Deliverables

Prefer producing these files:

1. Single-page balanced mind map PDF.
2. PNG preview.
3. SVG vector version.
4. Editable source file, preferably LaTeX/TikZ.
5. ZIP package containing all outputs.
6. Optional A4 split-print PDF if the single-page map is too dense.

## Content selection rules

The map should be “complete but not too fine.”

For each chapter section:

1. Keep the central concept.
2. Include the essential definition, theorem, criterion, or formula.
3. Include standard procedures when they are useful for exercises.
4. Omit long proofs unless the proof method is itself a testable technique.
5. Omit repetitive examples unless an example introduces a standard algorithm.
6. Prefer operational formulas over vague labels.

A good leaf node has this structure:

```text
正交矩阵
P^TP=E ⇔ 列/行向量为标准正交组；
P^{-1}=P^T，|P|=±1。
```

Avoid nodes that contain only a topic name, such as:

```text
正交矩阵
```

## Node density limits

For a single-page map, use these default limits:

- Each major section: 4–7 leaf nodes.
- Each leaf node: normally 2–3 lines, maximum 4 lines.
- Each line: preferably 12–24 Chinese characters, or one compact formula.
- One side of the map: preferably no more than 17–18 leaf nodes.
- Whole map: preferably no more than 34–36 leaf nodes.

If the chapter needs more content than this, do not squeeze everything into one page. Instead:

- merge low-priority details;
- shorten long formulas;
- move examples to fewer nodes;
- or create an A4 split-print version / two-page version.

## Formula rendering rules

Final visual outputs must render formulas correctly. Do not leave raw LaTeX commands visible in PDF/SVG/PNG.

Bad visual output examples:

- `\lambda`
- `\sum`
- `\sqrt{...}`
- `x^T A x` shown as source text because math was not rendered

Acceptable visual output examples:

- `Ax=λx，x≠0`
- `|A−λE|=0`
- `P^TAP=diag(λ₁,…,λₙ)`
- `f=x^TAx`
- `x=Cy ⇒ f=y^T(C^TAC)y`
- `Δ₁>0，Δ₂>0，…，Δₙ>0`

When using LaTeX/TikZ:

- Put formulas inside `$...$` or displayed math only when the node has enough space.
- Prefer compact inline formulas inside nodes.
- Avoid large displayed matrices in ordinary leaf nodes; they often create very tall nodes.
- If a matrix is necessary, either make that node a “tall node” with extra spacing, or simplify it into a verbal rule.

## Line break rules

All intended line breaks must be actual line breaks.

Never write node text like this in visual source:

```latex
\node {正交矩阵\n$P^TP=E$};
```

Write actual line breaks, for example:

```latex
\node[leaf] {
  \textbf{正交矩阵}\\
  $P^TP=E \Leftrightarrow$ 列/行向量为标准正交组；\\
  $P^{-1}=P^T,\ |P|=\pm1$。
};
```

Before finalizing, check the rendered PDF/PNG/SVG and confirm no visible `\n` remains.

## Balanced layout rules

Use a two-sided layout.

1. Put the chapter title in the center.
2. Place about half of the major sections on the left and half on the right.
3. Balance by visual weight, not only by section count.
   - A section with many leaves or tall formula nodes is heavier.
   - A section with large matrices is much heavier than a section of short definitions.
4. Keep branch direction consistent:
   - left-side sections extend leftward;
   - right-side sections extend rightward.
5. Do not place all branches on the right.
6. Do not rely on a uniform fixed vertical spacing such as `1.2cm` between every leaf node.

For a 7-section chapter, a default distribution is:

- Left: §1, §2, §3, §4
- Right: §5, §6, §7

For a 5-section chapter, a default distribution is:

- Left: §1, §2, §3
- Right: §4, §5

But adjust if one side has much taller nodes.

## Critical anti-overlap rule

Do not place leaf nodes with a manually fixed uniform y-grid unless every node has exactly the same height.

This is the main cause of overlaps. Nodes with formulas, matrices, fractions, or 4 lines of text become taller than ordinary nodes. If all nodes are placed at fixed y-coordinates such as `8.4, 7.2, 6.0, ...`, tall nodes will collide with the next node.

Use one of these collision-safe layout methods instead.

### Method A: TikZ relative-positioning vertical chains

For each section, stack its leaf nodes using `below=<gap> of previous node`, not fixed y-coordinates.

Example:

```latex
\node[leaf={cA}] (l11) at (-13,8.2) { ... };
\node[leaf={cA}, below=4mm of l11] (l12) { ... };
\node[leaf={cA}, below=4mm of l12] (l13) { ... };
```

This makes TikZ calculate the next node position from the actual bounding box of the previous node, so taller nodes automatically push later nodes downward.

### Method B: Section blocks

Treat each major section as a vertical block:

1. Stack all leaves of the section with relative positioning.
2. Put the section title node at the vertical midpoint of its first and last leaf.
3. Start the next section block below the previous block’s last leaf with a larger group gap.

Recommended gaps:

- leaf-to-leaf gap: at least `3.5mm`; use `5mm` for dense math chapters.
- section-to-section block gap: at least `8mm`; use `10–12mm` if a section contains tall nodes or matrices.
- root-to-section horizontal gap: at least `2.0cm`.
- section-to-leaf horizontal gap: at least `2.0cm`.

### Method C: Estimate node heights before assigning coordinates

If coordinates must be generated manually, estimate each node’s required height first.

A practical estimate:

```text
node_height_cm = 0.55 + 0.34 × text_line_count + math_penalty
```

Use these penalties:

- simple inline formula: `+0.05`
- fraction / subscript-heavy formula: `+0.10`
- displayed matrix: `+0.55` to `+1.10`
- four-line node: `+0.20`

Then set vertical distance between node centers as:

```text
center_gap = (height_current + height_next)/2 + 0.35cm
```

For section blocks, add at least `0.8cm` between the last leaf of one section and the first leaf of the next section.

## Connector rules to reduce visual clutter

Avoid drawing many direct diagonal arrows from a section node to every leaf when the section has more than four leaves. Direct fan-out connectors can cross or visually merge.

Use a small hub/bus connector instead:

```latex
\coordinate (h1) at ($(s1.west)+(-0.7,0)$);
\draw[branch={cA}] (s1.west) -- (h1);
\foreach \n in {l11,l12,l13,l14,l15,l16}{
  \draw[link, branch={cA}] (h1) |- (\n.east);
}
```

For right-side sections:

```latex
\coordinate (h4) at ($(s4.east)+(0.7,0)$);
\draw[branch={cD}] (s4.east) -- (h4);
\foreach \n in {r41,r42,r43,r44,r45,r46}{
  \draw[link, branch={cD}] (h4) |- (\n.west);
}
```

This makes branch lines cleaner and prevents arrow lines from crossing through nearby node text.

## Recommended TikZ implementation pattern

Use LaTeX/TikZ when formulas matter.

Recommended packages:

```latex
\documentclass[tikz,border=8pt]{standalone}
\usepackage{ctex}
\usepackage{amsmath,amssymb,bm}
\usetikzlibrary{arrows.meta,calc,positioning,shadows}
```

Recommended node styles:

```latex
\begin{tikzpicture}[
  x=1cm,y=1cm,
  every node/.style={align=center},
  root/.style={
    rounded rectangle, draw, thick, fill=black!85, text=white,
    text width=4.2cm, minimum height=1.25cm, inner sep=6pt,
    font=\bfseries\large
  },
  sec/.style args={#1}{
    rounded rectangle, draw=#1!80!black, thick, fill=#1!18,
    text width=3.7cm, inner sep=4pt, font=\bfseries\small
  },
  leaf/.style args={#1}{
    rounded rectangle, draw=#1!75!black, fill=#1!6,
    text width=5.8cm, inner sep=4pt, font=\scriptsize,
    align=center
  },
  link/.style={-{Stealth[length=2mm,width=1.5mm]}, line width=.55pt, draw=black!50},
  branch/.style={line width=.7pt, draw=#1!70!black}
]
```

### Collision-safe section example

This is the preferred pattern. Notice that only the first leaf has a fixed coordinate; later leaves use `below=... of ...`.

```latex
% root
\node[root] (root) at (0,0) {第6章\\线性空间与线性变换};

% left section leaf chain
\node[leaf={cA}] (l11) at (-13,8.2) {\textbf{基本结构}\\非空集 $V$ 上定义加法与数乘；\\元素称向量，运算未必是通常坐标运算。};
\node[leaf={cA}, below=4mm of l11] (l12) {\textbf{八条公理}\\加法交换、结合；零元、负元；\\$1\alpha=\alpha$，结合律与两条分配律。};
\node[leaf={cA}, below=4mm of l12] (l13) {\textbf{判断空间}\\先看封闭性：$\alpha+\beta\in V$，$\lambda\alpha\in V$；\\再核对公理。};

% section title at midpoint of leaf group
\coordinate (s1mid) at ($(l11.east)!0.5!(l13.east)$);
\node[sec={cA}] (s1) at ($(s1mid)+(2.0,0)$) {\S1 线性空间\\定义、例子与子空间};

% hub connector
\coordinate (h1) at ($(s1.west)+(-0.7,0)$);
\draw[link, branch={cA}] (root.west) -- (s1.east);
\draw[branch={cA}] (s1.west) -- (h1);
\foreach \n in {l11,l12,l13}{\draw[link, branch={cA}] (h1) |- (\n.east);}
```

For the next section on the same side, start its first leaf relative to the previous section’s last leaf:

```latex
\node[leaf={cB}, below=10mm of l13] (l21) { ... };
```

This prevents different section blocks from colliding.

## Handling tall formula or matrix nodes

Tall math nodes are the most common source of overlap. Apply these rules:

1. Avoid full displayed matrices unless they are essential.
2. If a matrix is essential, make the node text shorter and give the following node extra gap:

```latex
\node[leaf={cE}, below=5mm of r53] (r54) {\textbf{微分矩阵例}\\基 $(x^3,x^2,x,1)$ 下，$D$ 的矩阵为\\$\begin{pmatrix}0&0&0&0\\3&0&0&0\\0&2&0&0\\0&0&1&0\end{pmatrix}$。};
\node[leaf={cE}, below=8mm of r54] (r55) { ... };
```

3. If a matrix node still looks too tall, replace it with a compact description:

```text
微分矩阵例
基 (x³,x²,x,1) 下，D(x³)=3x²，D(x²)=2x；
矩阵列由各基向量的像坐标组成。
```

4. Never compress tall nodes by reducing vertical spacing below `3mm`.

## Preflight layout check

Before compiling, perform a layout preflight.

Check these items:

- Does any side have more than 18 leaf nodes?
- Does any section have more than 7 leaf nodes?
- Does any leaf node exceed 4 lines?
- Does any node contain a displayed matrix or long fraction?
- Are section blocks separated by at least `8mm`?
- Are leaf nodes stacked with relative positioning or computed gaps rather than a fixed uniform y-grid?

If any answer indicates risk, simplify content or create a larger/split version.

## Post-render quality check

After compiling/exporting, inspect the PNG or PDF.

Reject and revise the output if any of the following appears:

- leaf nodes overlap;
- leaf nodes touch or nearly touch;
- connector lines pass through text;
- one section’s leaves visually merge with the next section;
- literal `\n` appears;
- raw LaTeX commands appear;
- formulas are too small to read;
- the map is visually one-sided.

If overlaps are found, fix in this order:

1. Increase leaf-to-leaf gap.
2. Increase section block gap.
3. Shorten or split tall nodes.
4. Move one heavy section to the other side.
5. Make the canvas taller.
6. Produce split-page output instead of forcing one page.

## Final response format

When returning the finished mind map, briefly state the visual checks that were satisfied:

- left/right branches are balanced;
- node line breaks are real line breaks;
- formulas are rendered;
- no overlaps were found in the preview.

Then provide file links:

- PDF
- PNG
- SVG
- source file
- ZIP package

Do not add a legend unless requested.
Do not over-explain the production process unless the user asks.
