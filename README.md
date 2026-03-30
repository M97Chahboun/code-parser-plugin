# code-parser

> Index Dart, Python, and TypeScript codebases to cut LLM token consumption by **70–97%**.

code-parser extracts every class, interface, mixin, enum, and method — with exact line ranges — so Claude reads only the code that matters instead of loading entire files.

## Installation

```bash
/plugin install code-parser
```

Or from GitHub:

```bash
/plugin install https://github.com/M97chahboun/code-parser-plugin
```

## Requires

### Install with Cargo

```bash
cargo install code-parser
```

### Install with curl

```bash
curl -fsSL https://raw.githubusercontent.com/m97chahboun/code-parser/master/install.sh | bash
```

### Build from source

**Requirements:**

- Rust 1.75+ (1.77+ recommended)
- Cargo (bundled with Rust via rustup)

```bash
git clone https://github.com/m97chahboun/code-parser
cd code-parser
cargo build --release
# binary at: ./target/release/code-parser
```

## Commands

| Command | Arguments | Purpose |
|---|---|---|
| `/cp-index` | `[path]` | Index a file or directory — shows all classes, methods, line ranges |
| `/cp-find` | `<name> [path]` | Locate any class or method — returns file + exact lines |
| `/cp-read` | `<file> <start> <end>` | Read specific lines (also accepts `ClassName` or `Class.method`) |
| `/cp-edit` | `<file> <Class.method> <instruction>` | Surgical edit via index — reads only relevant lines |
| `/cp-audit` | `[path]` | Full architecture report — god classes, sizes, structure overview |
| `/cp-stats` | `[path]` | Estimated token savings for the session |

## Agents

| Agent | Use |
|---|---|
| `@code-navigator` | General codebase tasks — understands, edits, explains code efficiently |
| `@code-reviewer` | PR and diff review — reads only changed methods, not whole files |

## Skill

The bundled `code-parser` skill teaches Claude to **automatically** index before reading any `.dart`, `.py`, `.ts`, or `.tsx` file — no command needed.

## How it works

```
Naive approach:    read full file  → ~15,000 tokens for a 2,000-line file
With code-parser:  index + targeted reads → ~700 tokens for the same task
                                             ↑ 95% reduction
```

### Two-phase workflow

**Phase 1 — Index (cheap)**  
Run `code-parser` once. Get every class/method name and line range. ~400 tokens.

**Phase 2 — Targeted reads (surgical)**  
Use `sed -n 'start,endp' file` to fetch only the relevant method bodies. ~100–300 tokens per method.

## Example session

```
> /cp-index ./lib
  Indexed 24 files · 38 classes · 187 methods

> /cp-find UserService
  📁 lib/services/user_service.dart
  class UserService  lines 8–47
    fetchUser    lines 15–22
    deleteUser   lines 24–31

> /cp-read lib/services/user_service.dart 15 22
  [shows 8 lines of code]
  (saved ~2,600 tokens vs reading the full file)

> /cp-stats
  🎯 Estimated saving: ~94%
```

## Supported languages

| Language | Extensions | Detects |
|---|---|---|
| Dart | `.dart` | class, abstract class, mixin, extension, enum |
| Python | `.py` | class (all decorator types) |
| TypeScript | `.ts`, `.tsx` | class, abstract class, interface, enum |

## License

MIT
