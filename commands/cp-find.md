---
description: Find where a class or method is defined in a Dart, Python, or TypeScript project. Returns the file path and exact line range without reading any source. Use before reading any code to pinpoint exactly what to fetch.
allowed-tools: Bash
---

Find the symbol "$ARGUMENTS" using code-parser.

## Instructions

Parse the argument: first word is the symbol name, optional second word is the path (default: `.`).

Run code-parser and search with jq:

```bash
# Search by class name (exact)
code-parser ${PATH:-.} --format json | jq --arg n "$SYMBOL" '
  .[] | .file as $f |
  .classes[] | select(.name == $n) |
  {file: $f, name, kind, line_start, line_end,
   methods: [.methods[] | {name, line_start, line_end}]}
'

# Search by method name
code-parser ${PATH:-.} --format json | jq --arg n "$SYMBOL" '
  .[] | .file as $f |
  .classes[] | .name as $c |
  .methods[] | select(.name == $n) |
  {file: $f, class: $c, method: .name, line_start, line_end}
'

# Fuzzy partial match
code-parser ${PATH:-.} --format json | jq --arg n "${SYMBOL,,}" '
  .[] | .file as $f |
  .classes[] | select(.name | ascii_downcase | contains($n)) |
  {file: $f, name, kind, line_start, line_end}
'
```

Present results as:

```
Found: UserService
  📁 lib/services/user_service.dart
  Kind: class  |  Lines: 8–47
  Methods:
    fetchUser      lines 15–22
    deleteUser     lines 24–31
    updateUser     lines 33–41

To read a method:
  sed -n '15,22p' lib/services/user_service.dart
```

If nothing found, suggest checking spelling or running `/cp-index` first.
