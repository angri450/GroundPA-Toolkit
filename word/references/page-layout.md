# Word Document Page Layout & Compaction

Reference for `nong word` compaction pipeline and OOXML pagination controls. Load when the user asks to tighten layout, remove blank space, fix split tables, rearrange images, or control page breaks.

## Core Commands

| Command | Purpose | Read-only |
|---------|---------|-----------|
| `word estimate` | Analyze page breaks, measure waste per page | yes |
| `word crop` | Auto-crop blank margins from images | no |
| `word fit-images` | Merge adjacent image paragraphs + scale to fit page width | no |
| `word compact-tables` | Tables: 100% wide, centered, equal columns, remove fixed row heights | no |
| `word regroup-images` | Cross-section orphan image pairing (wider scan) | no |
| `word page-setup` | Page size, orientation, margins, columns, different first page | no |
| `word indent` | First-line, hanging, left, right indentation by role | no |
| `word paragraph-control` | keepNext, keepLines, pageBreakBefore, widowControl by role | no |

## Typical Pipeline

```powershell
# 1. Analyze waste
nong word estimate <file.docx> --json

# 2. Fix images
nong word fit-images <file.docx> -o <out.docx> --json
nong word crop <file.docx> -o <out.docx> --json

# 3. Fix tables
nong word compact-tables <file.docx> -o <out.docx> --json

# 4. Fix orphans
nong word regroup-images <file.docx> -o <out.docx> --json

# 5. Verify
nong word estimate <out.docx> --json
```

## OOXML Pagination Controls

Word has four paragraph-level page controls. `word paragraph-control` and `word compact-tables` manage them.

| OOXML element | Word UI name | Effect |
|---------------|-------------|--------|
| `w:keepNext` | 与下段同页 | Prevents break between this para and next |
| `w:keepLines` | 段中不分页 | Prevents para from splitting across pages |
| `w:pageBreakBefore` | 段前分页 | Forces new page before this para |
| `w:widowControl` | 孤行控制 | Prevents single line at top/bottom of page |

Row-level table control:

| OOXML element | Effect |
|---------------|--------|
| `w:cantSplit` | Prevents row from splitting across pages |

`word compact-tables` preserves existing keepNext/keepLines and adds keepNext to header rows (bold first cell or `w:tblHeader`). It adds cantSplit to rows with multi-line content.

## Table Width vs Page Breaks

Tables with `w:tblW w:type="auto"` let columns shrink to shortest text — forcing long-content columns to wrap excessively, inflating row heights. `word compact-tables` sets `w:type="pct" w:w="5000"` (100% page width) and equalizes columns. This drops row height, allowing tables to fit on one page instead of two.

## Auto-Height Mode

`word compact-tables --auto-height` removes ALL row height constraints (`exact`/`atLeast` → `auto`), letting cell content determine height with zero fixed limits.

## Indentation

`word indent` supports four indent types + outline level, targeted by paragraph role:

```powershell
# Body text: 2-character first-line indent
nong word indent <file.docx> --role body --first-line 7.4 -o <out.docx>

# Hanging indent for bibliography
nong word indent <file.docx> --role body --hanging 7 -o <out.docx>

# Outline level for TOC
nong word indent <file.docx> --role heading --outline-level 1 -o <out.docx>
```

## Page Setup

`word page-setup` handles section-level properties:

```powershell
# B5 sizing with narrow margins
nong word page-setup <file.docx> --size B5 --margin-top 15 --margin-bottom 15 -o <out.docx>

# Landscape orientation
nong word page-setup <file.docx> --orient landscape -o <out.docx>

# Two-column layout
nong word page-setup <file.docx> --columns 2 --column-gap 5 -o <out.docx>
```
