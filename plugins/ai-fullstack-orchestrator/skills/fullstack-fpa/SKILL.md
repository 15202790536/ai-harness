---
name: fullstack-fpa
description: >
  Language-agnostic Function Point Analysis (FPA). Counts function points from design docs,
  frontend pages, and backend APIs. Produces structured FPA reports.
version: 1.0.0
author: AI Orchestrator
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
---

# fullstack-fpa — Function Point Analysis

<objective>
Perform Function Point Analysis on design documents and generated code. Produce a
structured FPA report counting data functions and transactional functions.
</objective>

<process>

## Pre-requisites
- Design docs or change plans in 设计输入/03-详细设计/
- Implemented frontend pages
- Implemented backend APIs

## Counting Rules

### Data Functions
- **ILF (Internal Logical File)**: Each database table = 1 ILF
- **EIF (External Interface File)**: Each external API consumed = 1 EIF

### Transactional Functions
- **EI (External Input)**: Create/Update/Delete operations
- **EO (External Output)**: List/Detail queries with data transformation
- **EQ (External Query)**: Simple queries without transformation

### Complexity Matrix
Based on DET (Data Element Types) and RET/FTR counts:
- Low: 1-5 DET, 1 RET
- Medium: 6-15 DET, 2 RET
- High: 16+ DET, 3+ RET

## Output

Write to 设计输入/02-需求分析/功能点分析报告.md with:
- Per-module FP counts
- Frontend FP (per page)
- Backend FP (per API)
- Total adjusted FP

## Naming Convention
- Frontend: [module]-[operation] frontend (e.g., Order-Query Orders frontend)
- Backend: [module]-[operation] (e.g., Order-Create Order)
</process>