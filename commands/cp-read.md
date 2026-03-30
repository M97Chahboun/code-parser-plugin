---
description: Read specific lines from a source file using the code-parser index. Accepts a line range (file start end), a class name (file ClassName), or a method (file ClassName.method). Far cheaper than loading the whole file.
allowed-tools: Bash
---

Read targeted lines. Arguments: $ARGUMENTS

## Instructions

Parse the arguments into one of these forms:
- **`file start end`** — read lines start–end directly
- **`file ClassName`** — look up the class in the index, read its line range
- **`file ClassName.method`** — look up the method, read just that method
- **`file methodName`** — search all classes for the method name

**For a direct line range:**
```bash
sed -n 'START,ENDp' FILE
```

**For a class or method name lookup, index first:**
```bash
code-parser FILE --format json | jq '
  .[0].classes[] | select(.name == "CLASSNAME") |
  {line_start, line_end, methods}
'
# Then: sed -n 'line_start,line_endp' FILE
```

**Rules:**
- Never read more than 120 lines at once unless explicitly asked
- For classes longer than 120 lines: read the header (first 20 lines) and list methods, then ask which body to load
- Always show the file and line range before the code block
- End with the token saving estimate: `(read N lines of M-line file — saved ~X tokens)`
