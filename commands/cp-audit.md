---
description: Audit a Dart, Python, or TypeScript project structure with code-parser. Produces a full architecture report including class sizes, method counts, god classes, and structural overview. Run on any project before a refactor or code review.
allowed-tools: Bash
---

Audit the project at: $ARGUMENTS (default: current directory)

## Instructions

### 1. Collect data
```bash
code-parser ${ARGUMENTS:-.} --format json > /tmp/cp_audit.json
```

### 2. Run these jq queries

```bash
# Totals
jq '{files: length, classes: [.[].classes | length] | add,
     methods: [.[].classes[].methods | length] | add}' /tmp/cp_audit.json

# God classes (>15 methods)
jq '[.[] | .file as $f | .classes[] |
  select(.methods | length > 15) |
  {name, file: $f, methods: (.methods | length)}]' /tmp/cp_audit.json

# Large classes (>200 lines)
jq '[.[] | .file as $f | .classes[] |
  select((.line_end - .line_start) > 200) |
  {name, file: $f, lines: (.line_end - .line_start)}]' /tmp/cp_audit.json

# Empty classes
jq '[.[] | .file as $f | .classes[] |
  select(.methods | length == 0) |
  {name, kind, file: $f}]' /tmp/cp_audit.json

# Language breakdown
jq 'group_by(.language) |
  map({lang: .[0].language, files: length})' /tmp/cp_audit.json
```

### 3. Present the report

Structure it as:
```
# Project Audit — ./lib

## Summary
Files · Classes · Methods · Languages

## ⚠ Warnings
God classes, large bodies, anything suspicious

## ℹ Info
Empty classes, script-only files

## All Classes
Group by file, table of name / kind / method count / line range
```

### 4. Suggest next steps
Based on findings: which classes to refactor, which files are relevant to the user's task.
