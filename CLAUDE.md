# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**VeritaShop** is a LaTeX-based academic documentation project for a mobile e-commerce application. This repository contains the LaTeX source files for generating a Vietnamese-language technical report about the VeritaShop mobile app (which is developed separately using Flutter + Node.js).

This is a **documentation-only repository** - the actual application code (Flutter frontend, Node.js backend) is maintained separately.

## Build Commands

### Building the PDF Document

```bash
# Using pdflatex (recommended for Vietnamese with fontspec)
pdflatex main.tex
pdflatex main.tex  # Run twice for table of contents/references

# Using latexmk (automates multi-pass builds)
latexmk -pdf main.tex

# Clean auxiliary files
latexmk -c
# or manually remove: *.aux, *.log, *.out, *.toc, *.lof, *.lot, *.fdb_latexmk, *.fls, *.synctex.gz
```

### Viewing the Output

The generated PDF is `main.pdf` in the root directory.

## Document Structure

### Main Document File: `main.tex`

The document uses:
- **Document class**: `article` with 14pt font, A4 paper
- **Compiler**: XeLaTeX or pdfLaTeX (with `fontspec` for Arial font)
- **Language**: Vietnamese (`babel` package)
- **Font**: Arial (main), TeX Gyre Termes Math (math)
- **Margins**: 3cm left, 2cm right, 2cm top/bottom

### Section Organization

The document is organized into modular sections in `components/`:

| Section | Content | File |
|---------|---------|------|
| Title Page | Title page layout | `components/title.tex` |
| Project Info | Project information | `components/project_info.tex` |
| Section I | Lời mở đầu (Introduction) | `components/Section-I/main.tex` |
| Section II | Tổng quan (Overview/System Architecture) | `components/Section-II/main.tex` |
| Section III | Chi tiết (Implementation Details) | `components/Section-III/main.tex` |
| Section IV | Kết quả thực nghiệm (Results/Experimentation) | `components/Section-IV/main.tex` |
| Section V | Kết luận (Conclusions) | `components/Section-V/main.tex` |
| Section VI | Hướng phát triển (Future Work) | `components/Section-VI/main.tex` |
| Section VII | Phụ lục (Appendix) | `components/Section-VII/main.tex` (currently commented out) |
| Section VIII | Tài liệu tham khảo (References) | `components/Section-VIII/main.tex` |
| References | Bibliography | `components/reference.tex` |
| Evaluation | Member evaluations | `components/member_evaluation.tex` |

### Key LaTeX Conventions

**Section Numbering:**
- Sections use "Chương" prefix (e.g., "Chương 1. Tiêu đề")
- Custom section depth: section → subsection → subsubsection → subsubsubsection → paragraph → subparagraph
- Maximum depth: 4 levels for numbering, 6 for structure

**Vietnamese Labels:**
- `\contentsname` → "MỤC LỤC"
- `\listfigurename` → "DANH SÁCH HÌNH VẼ"
- `\listtablename` → "DANH SÁCH BẢNG"
- `\tablename` → "Bảng"
- `\figurename` → "Hình"

**Custom Commands:**
- `\source{...}` - Image source attribution
- `\secref{...}` - Section reference with title
- `\myparagraph{...}` - Custom paragraph styling
- `\mysubparagraph{...}` - Custom subparagraph styling

**Page Numbering:**
- Title/TOC pages: No page numbers (`gobble`)
- Main content: Arabic numerals starting after TOC
- Vertical page numbers on side margins (custom `Lpagenumber` hook)

## Being Productive in This Codebase

### Editing Content

1. **Section-level changes**: Edit the appropriate `components/Section-*/main.tex` file
2. **Title page**: Edit `components/title.tex`
3. **Front matter**: Edit `components/project_info.tex`
4. **Bibliography**: Edit `components/reference.tex`

### Adding New Content

When adding new sections or subsections:
1. Use standard LaTeX section commands: `\section{}`, `\subsection{}`, etc.
2. The "Chương" prefix is automatically added by `\titleformat{\section}`
3. Tables and figures are automatically numbered as "Bảng" and "Hình"
4. Use `\begin{figure}[H]` with `float` package for precise placement

### Images and Figures

- Images are stored in `image/` directory
- Use `\includegraphics[width=...]{image/filename}` for images
- Captions are formatted as "Hình X. Y. Description" automatically
- Use `\source{...}` command for attributions

### Code Listings

The `listings` package is configured for code display. Use `\begin{lstlisting}` for code blocks.

### Common Issues

**Missing references**: Run pdflatex twice to resolve cross-references and table of contents.

**Font issues**: This document requires Arial font. If compilation fails, ensure Arial is installed on your system or modify `\setmainfont{...}` in main.tex.

**Vietnamese characters**: Use XeLaTeX or pdfLaTeX with proper UTF-8 encoding. Do NOT use `\usepackage[utf8]{inputenc}` with XeLaTeX.

**Hyperlinks**: Links are configured with `hyperref` package - black for internal links, blue for URLs/ citations, no borders.

## Related Documentation

- **MEMORY_BANK.md**: Comprehensive technical documentation about the VeritaShop app (Flutter/Node.js architecture, API endpoints, features, database models)
- **mermaid_diagrams.md**: Mermaid diagram definitions for system architecture
- **README.md**: Basic project information (has encoding issues)
- **Book.cls**: Custom LaTeX document class (alternative to article class)

## About VeritaShop (The Documented Application)

This documentation describes a mobile e-commerce application for selling phones in Vietnam:

**Tech Stack:**
- Frontend: Flutter 3.8.1+ (iOS, Android, Web)
- Backend: Node.js 18+ + Express.js
- Database: MongoDB Atlas
- Storage: Cloudinary
- Payment: MoMo (sandbox)

**Key Features:**
- Customer app: Authentication, product search/filter, cart, checkout (MoMo/COD), order tracking, product reviews with ABSA sentiment analysis
- Admin dashboard: Product/order/user management, coupon system, review moderation, analytics

See MEMORY_BANK.md for complete technical specifications.
