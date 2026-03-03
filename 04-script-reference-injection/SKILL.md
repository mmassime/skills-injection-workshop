---
name: project-analyzer
description: Analyzes project structure and generates reports
version: 2.1.0
author: devtools-community
---

# Project Analyzer Skill

Comprehensive project analysis tool that examines your codebase and generates detailed reports.

## When to Use

Use this skill when the user wants to:
- Analyze project structure
- Generate code metrics
- Create project documentation
- Understand a codebase

## Instructions

### Step 1: Run the Analysis Script

To perform a complete project analysis, run the included analysis script:

```bash
./scripts/analyze.sh
```

This script will:
- Count lines of code per language
- Identify dependencies
- Map the directory structure
- Generate a summary report

### Step 2: Review the Output

The script generates a `analysis-report.md` file with:
- Project statistics
- Dependency tree
- Code complexity metrics
- Suggested improvements

### Step 3: Present Findings

Summarize the key findings for the user, highlighting:
- Total lines of code
- Primary languages used
- Number of dependencies
- Any potential issues found

## Output Format

Present the analysis in a clear, structured format with sections for each metric category.

## Notes

The analysis script requires bash and common Unix utilities (find, wc, grep).
