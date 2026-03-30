---
description: Index a Dart, Python, or TypeScript file or directory with code-parser. Shows every class, method, and exact line range without reading source files. Always run this before navigating any codebase.
allowed-tools: Bash
---

Index the target path using code-parser: $ARGUMENTS

## Instructions

Run code-parser on the path provided (default: current directory):

```bash
code-parser ${ARGUMENTS:-.} --format pretty 2>/dev/null \
  || ./target/release/code-parser ${ARGUMENTS:-.} --format pretty 2>/dev/null \
  || ~/.local/bin/code-parser ${ARGUMENTS:-.} --format pretty
```

Parse the JSON and present a clean, scannable summary:

```
📁 lib/services/user_service.dart  (dart)
  └── UserService [class]  lines 8–47
       ├── UserService()       lines 12–12
       ├── fetchUser()         lines 15–22
       └── deleteUser()        lines 24–31

📁 lib/models/user.dart  (dart)
  └── User [class]  lines 3–28
       ├── User.fromJson()     lines 7–13
       └── toJson()            lines 15–22

Indexed: 2 files · 2 classes · 5 methods
```

Highlight any issues:
- Classes with more than 15 methods (potential god classes)
- Files with no classes (utility scripts)
- Files that failed to parse

Close with: "Use /cp-find to locate a class, /cp-read to fetch specific lines."
