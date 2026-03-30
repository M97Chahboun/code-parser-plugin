---
description: Surgically edit a class or method in a Dart, Python, or TypeScript file. Indexes the file first, reads only the relevant lines, then applies the change. Use instead of reading the whole file before editing.
allowed-tools: Bash, Read, Edit
---

Perform a targeted edit. Arguments: $ARGUMENTS

Expected: `file ClassName.method instruction` or natural description.

## Instructions

### Step 1 — Index the file only (not the directory)
```bash
code-parser FILE --format json
```

### Step 2 — Find the target line range
From the index, get `line_start` and `line_end` of the class or method to edit.

### Step 3 — Read only those lines
```bash
sed -n 'line_start,line_endp' FILE
```
Read neighbouring methods only if context is needed to understand the change.

### Step 4 — Apply the edit
Use the Edit tool to modify exactly those lines. Do not rewrite the full file.

Show a clear before/after diff of just the changed section.

### Step 5 — Verify
Re-read the edited lines to confirm correctness:
```bash
sed -n 'line_start,line_endp' FILE
```

**Rules:**
- Never load the full file if it is longer than 60 lines
- Never rewrite the whole file to make a small change
- Always report the token saving at the end
