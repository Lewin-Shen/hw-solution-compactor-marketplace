---
name: hw-solution-compactor
description: "Compact homework problems and their solutions into dense, space-efficient PDF reference sheets via LaTeX. Use this skill whenever the user wants to: compress or compact homework solutions for exam prep, turn worked problem sets into a dense study reference, fit homework solutions onto minimal pages, make a tight reference sheet from worked examples, or shrink multi-page solutions into a compact PDF. Also trigger when the user mentions 'compact homework solutions', 'compact my HW', 'fit solutions on one page', 'dense solution PDF', 'prepare for exam cheatsheet', or wants to squeeze homework problem-and-answer pairs into minimal space. Works with PDF homework files, images of solutions, or problem/solution text provided in conversation."
---

# HW Solution Compactor

Turn multi-page homework solutions into dense, space-efficient PDF reference sheets that preserve all mathematical content while eliminating whitespace. Designed so students can screenshot individual solutions and paste them into their own cheat sheets, or use the entire output directly.

## When to Use

This skill is for students (or anyone) who want to take existing worked solutions -- from homework, textbook problems, practice exams -- and compress them into the most space-efficient PDF possible, typically for use as an allowed cheat sheet on exams. The key constraint: **all math equations and derivations must be preserved faithfully**. Only whitespace and formatting fluff gets removed.

## How It Works

The approach is LaTeX-based. You read the source material, extract the mathematical content, and generate a `.tex` file with aggressive space-saving settings, then compile it to PDF. This consistently produces better results than trying to rearrange content in Word or reformat an existing PDF, because LaTeX gives precise control over every spacing parameter.

## Step-by-Step Workflow

### 1. Understand the Input

Read the files the user provides. There are two common scenarios:

**Single file**: One PDF/image contains both problems and solutions together. This is the simpler case -- just read through it.

**Separate files**: The problems and solutions come in two different files (e.g., "HW3.pdf" and "HW3_Solutions.pdf"). When this happens, read both files and **match each solution to its corresponding problem**. Sometimes the solutions file skips the problem statement and jumps straight to the answer, so you need the problem file to get the setup, given values, and what's being asked. Cross-reference by problem number. If the numbering doesn't line up or is ambiguous, ask the user to clarify.

For each file, identify:
- How many problems there are
- Which problems the user wants included (they may want to skip some)
- What content to exclude (diagrams, graphs, extra credit, certain sub-problems)

When reading PDFs, use the `Read` tool with the `pages` parameter. For images, view them directly.

### 2. Ask About Preferences

Before generating, clarify these with the user (unless they've already specified):

**Target page count**: Ask the user how many pages they want. Suggest a default based on problem count (see guidelines below), but let them choose. If the user is unsure, tell them: "I'll generate with the default detail level and you can tell me to add more or less after you see it."

**Layout**: Ask whether they prefer:
- **Horizontal (single column)** -- each problem flows top-to-bottom in full page width. Best when the user plans to screenshot individual problems and paste them into their own cheat sheet, since each problem is a clean rectangular block. This is the default.
- **Vertical columns (2-column)** -- content flows in two columns. Good for fitting more on one page when there are many short problems.
- **Vertical columns (3-column)** -- very dense three-column layout. Best for maximizing raw content per page, but equations can get cramped. Works well when problems have mostly short formulas.

If the user doesn't have a preference, default to **horizontal (single column)** since it's the most versatile and makes clean screenshots.

### 3. Extract Content

For each problem to include, extract:
- **Problem statement**: Compress to 1-3 lines. Keep the physical setup, given values, and what's being asked. Drop verbose preamble like "In this problem you will explore..." but keep the actual physics/engineering context.
- **Key assumptions**: If the solution states assumptions, keep them -- they often matter for understanding why a particular approach was taken.
- **Solution steps**: Keep every equation, intermediate result, and enough connecting text that someone can follow the logic. The default level of detail should lean toward preserving more of the original solution text rather than aggressively summarizing it. A student under exam pressure needs to be able to follow the reasoning, not just see the formulas.
- **Final answers**: These get `\boxed{}` treatment so they stand out when scanning the cheat sheet under exam pressure.
- **Data tables**: Keep if they contain values needed for the solution. Use `booktabs` for compact tables.

### 4. Generate the LaTeX Document

Use this preamble -- it's been tuned through iteration to maximize density:

```latex
\documentclass[7pt]{article}
\usepackage[margin=0.2in,top=0.15in,bottom=0.15in]{geometry}
\usepackage{amsmath,amssymb}
\usepackage{titlesec}
\usepackage{parskip}
\usepackage{booktabs}

\setlength{\parskip}{0.5pt}
\setlength{\parindent}{0pt}
\setlength{\abovedisplayskip}{1.5pt}
\setlength{\belowdisplayskip}{1.5pt}
\setlength{\abovedisplayshortskip}{0.5pt}
\setlength{\belowdisplayshortskip}{0.5pt}

\titleformat{\section}{\small\bfseries}{}{0em}{}
\titlespacing*{\section}{0pt}{3pt}{1pt}

\pagestyle{empty}
```

For **multi-column layouts**, add `\usepackage{multicol}` and wrap the body:
```latex
% 2-column
\begin{multicols}{2} ... \end{multicols}

% 3-column
\begin{multicols}{3} ... \end{multicols}
```

These preamble settings matter and here's why:
- **7pt font**: The smallest readable size. Going to 6pt makes equations hard to read under exam stress.
- **0.2in margins, 0.15in top/bottom**: Maximizes usable area without content getting clipped by printers.
- **Display skip 1.5pt**: Equations need *some* breathing room or they merge visually with surrounding text, but the default ~12pt is wasteful.
- **Empty page style**: No headers/footers eating space.

### 5. Space-Saving Techniques

These are the tricks that make the difference between fitting within the target page count or spilling over:

**Inline math over display math.** The single biggest space saver. A display equation like:
```latex
\[ F = ma \]
```
takes an entire line plus vertical padding. Inline `$F = ma$` flows with the text. Use display mode only for multi-line derivations or equations complex enough to be unreadable inline.

**Chain derivations on one line.** Instead of showing each step on its own line:
```latex
% BAD - wastes 3 lines
\[ x = \frac{a}{b} \]
\[ = \frac{3}{4} \]
\[ = 0.75 \]

% GOOD - one line
$x = \frac{a}{b} = \frac{3}{4} = 0.75$
```

**Compress problem statements.** The original might say "A 2 MeV neutron beam is used to irradiate a stainless steel (SS304) sample. Assume the steel is primarily iron (Fe, A=56). Estimate the number of displacements per primary knock-on atom..." Compress to: "2 MeV neutron $\to$ SS304 (Fe, $M=56$). $E_d=40$ eV. Find displacements per PKA."

**Use `\\[1pt]` or `\\[2pt]` for line breaks** instead of blank lines or `\vspace`. This gives just enough visual separation between sub-problems.

**`\boxed{}` for final answers.** Wrap key results: `$\boxed{v_f = 12.4\text{ m/s}}$`. This serves double duty -- it highlights answers AND provides visual structure without needing extra whitespace.

**Abbreviate units and common terms.** After defining once: "displacement energy ($E_d$)", just use $E_d$ thereafter. Same for materials, isotopes, etc.

**Side-by-side results** when multiple cases are computed the same way:
```latex
\textbf{Al:} $\Delta T = \boxed{45.2\;^\circ\text{C}}$\quad
\textbf{Cu:} $\Delta T = \boxed{31.7\;^\circ\text{C}}$\quad
\textbf{SS:} $\Delta T = \boxed{68.9\;^\circ\text{C}}$
```

**Tables for data**, not for layout. If the solution references a table of values, reproduce it compactly with `booktabs` and `\small`.

### 6. Compile and Verify

```bash
pdflatex -interaction=nonstopmode <filename>.tex
```

After compilation, check the page count. If it overflows the target:
1. Convert more display equations to inline
2. Reduce `\parskip` to 0pt
3. Compress problem statements further
4. As a last resort, drop to 6pt font

If it's well under the target, that's fine -- let the user know and offer to add more detail if they want. Say something like: "This came out to 0.7 pages. I can add more detail from the original solutions if you'd like, or leave room for you to add your own notes."

### 7. Deliver

Save the compiled PDF and the `.tex` source to the user's workspace folder. The `.tex` file is important because:
- The user may want to tweak wording or add their own notes
- It's easy to recompile after edits
- They can combine it with other `.tex` cheat sheets

Let the user know they can ask to adjust detail level, layout, or which problems are included.

## Handling Different Input Types

**PDF homework solutions (single file)**: The most common case. Read with the `Read` tool, page by page. Extract math by transcribing equations into LaTeX.

**Separate problem + solution PDFs**: Read both files. Match problems to solutions by number. Use the problem file for the statement/setup and the solution file for the worked answer. If a solution references values or context from the problem statement that aren't restated in the solution, pull those from the problem file.

**Images/photos of solutions**: View the image directly. Transcribe the handwritten or printed math into LaTeX. Pay special attention to subscripts/superscripts and fraction bars that may be ambiguous in photos.

**Text/equations in conversation**: The user may paste or describe problems. Convert directly to the LaTeX template.

## Detail Level and Target Pages

The default detail level preserves most of the original solution's reasoning -- enough that a student can follow along step by step during an exam, not just see bare formulas. This means keeping intermediate steps, brief justifications (e.g., "Since $\bar{T} < E_c$, use the low-energy approximation"), and all numerical substitutions.

When the user specifies a target page count, adjust detail accordingly:
- **Fewer pages than default** → more aggressive compression: shorter problem statements, more inline math, skip intermediate substitution steps, just show key equations and final answers.
- **More pages than default** → preserve more original text, use display equations for clarity, include more explanatory notes between steps.

Default targets based on problem count:
- **1-3 problems**: 1 page
- **4-6 problems**: 1-1.5 pages
- **7-10 problems**: 1.5-2 pages
- **Full course cheat sheet**: 2 pages (front and back)

If the user doesn't specify and seems unsure, generate at the default level and tell them: "This is the default density -- let me know if you want me to pack it tighter or spread it out with more detail."

## What NOT to Change

The whole point is that the user trusts these solutions and wants them preserved for reference. Never:
- Simplify or skip intermediate steps unless the user explicitly asks or the target page count requires it
- "Correct" equations that look wrong -- reproduce faithfully and flag concerns separately
- Omit numerical values or final answers
- Paraphrase mathematical derivations into prose
