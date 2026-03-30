---
description: Show estimated token savings from using code-parser on a project. Compares full-file read cost against the index + targeted-read approach. Run at the end of a session to see efficiency gains.
allowed-tools: Bash
---

Show token saving stats for: $ARGUMENTS (default: current directory)

## Instructions

### 1. Count total source lines
```bash
find ${ARGUMENTS:-.} -type f \
  \( -name "*.dart" -o -name "*.py" -o -name "*.ts" -o -name "*.tsx" \) \
  | xargs wc -l 2>/dev/null | tail -1
```

### 2. Measure index size
```bash
code-parser ${ARGUMENTS:-.} --format json | wc -c
```

### 3. Calculate and display

Estimates:
- Tokens per line ≈ 8 (average ~32 chars / 4)
- Index tokens = JSON bytes / 4
- Typical session reads 10–15% of all methods

Present:
```
╔══════════════════════════════════════════════╗
║       code-parser Token Savings              ║
╠══════════════════════════════════════════════╣
║ Source files          │  N files             ║
║ Total lines           │  N lines             ║
╠══════════════════════════════════════════════╣
║ NAIVE (read all files)│  ~N tokens           ║
╠══════════════════════════════════════════════╣
║ WITH code-parser      │                      ║
║   Index               │  ~N tokens           ║
║   Targeted reads*     │  ~N tokens           ║
║   Total               │  ~N tokens           ║
╠══════════════════════════════════════════════╣
║ 🎯 Estimated saving   │  ~N%                 ║
╚══════════════════════════════════════════════╝
* Assumes reading ~12% of methods (typical session)
```

Language breakdown: count files and lines per language.
