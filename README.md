# HW Solution Compactor

A Claude Code plugin that compacts multi-page homework solutions into dense, space-efficient PDF reference sheets via LaTeX — perfect for exam cheat sheet prep.

## What It Does

Give it a homework solutions PDF and it will:

- Extract all problems, equations, and derivations
- Generate a LaTeX document with aggressive space-saving settings (7pt font, minimal margins, inline math)
- Compile to a compact PDF that fits on 1 page (or your target page count)
- Preserve all math faithfully — only whitespace and filler text gets removed
- Box final answers for quick scanning during exams

## Features

- **Separate file handling**: Works when problems and solutions come in different files — matches them automatically
- **Layout options**: Horizontal (single column, best for screenshotting), 2-column, or 3-column
- **Adjustable detail**: Specify target page count and the skill adjusts compression accordingly
- **Multiple input types**: PDF files, images/photos of solutions, or text pasted in chat

## Install

Add this marketplace to Claude Code:

```
/plugin marketplace add https://github.com/Lewin-Shen/hw-solution-compactor-marketplace.git
```

Or if you have SSH keys configured:

```
/plugin marketplace add Lewin-Shen/hw-solution-compactor-marketplace
```

Then install the plugin:

```
/plugin install hw-solution-compactor@hw-solution-compactor-marketplace
```

## Usage

Just provide your homework solutions and ask Claude to compact them:

- "Compact this homework solution into a 1-page cheat sheet"
- "Fit these HW solutions on one page for my exam"
- "Make a dense PDF from these solutions, skip the diagrams"

## Example

**Before**: 8-page homework solutions PDF with wide margins, large font, diagrams, and whitespace.

**After**: 1-page dense reference sheet with all equations preserved, boxed answers, and minimal spacing.

## Requirements

- Claude Code (or Cowork mode)
- LaTeX (`pdflatex`) available in the environment

## License

MIT
