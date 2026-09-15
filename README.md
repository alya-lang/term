# term

[![CI](https://github.com/alya-lang/term/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/term/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/alya-lang/term?color=blue&label=License)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fterm%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fterm%2Fmain%2Falya.toml&query=%24.package.version&label=Version&color=brightgreen)](alya.toml)

Modern, zero-dependency terminal UI toolkit for the Alya language ecosystem: ANSI styling, Unicode tables, callout boxes, tree hierarchies, progress bars, mini charts, status loggers, interactive prompts, and cursor controls.

---

## 🌟 Features

- ⚡ **Lightweight & Fast**: Built for speed with minimal overhead (>25M ops/sec for ANSI processing)
- 📊 **Dynamic Tables**: Auto-sizing columns, custom alignment (Left, Center, Right), multi-line cell support, and maximum column width truncation
- 🎨 **7 Border Presets**: `unicode` (┌─┬─┐), `rounded` (╭─┬─╮), `double` (╔═╦═╗), `ascii` (+-++), `markdown` (|---|), `compact`, and `none`
- 🌈 **ANSI & UTF-8 Aware**: Accurately measures printable visual width (`visible_len`) ignoring ANSI escapes and correctly counting multi-byte UTF-8 code points
- 🌳 **Tree Hierarchies**: Render nested directory trees and AST structures with Unicode connectors (`├── `, `└── `, `│   `)
- 📏 **Horizontal Rules & KV Lists**: Beautiful styled dividers (`hr()`) and aligned key-value summaries with dot leaders (`kv()`)
- 📈 **Mini Visualizations**: Compact inline sparklines (` ▂▃▄▅▆▇█`) and horizontal percentage bar charts
- 💬 **Callout Banners**: Message boxes with embedded titles and pre-styled helpers (`box_info`, `box_success`, `box_warn`, `box_error`)
- 🚦 **Status Loggers & Badges**: Standard CLI outputs with distinctive icons (`success`, `warn`, `error`, `info`, `step`)
- ⏳ **Progress & Spinners**: Customizable progress bars and animated spinner frames (`dots`, `line`, `pulse`, `arrows`, `blocks`)
- 🖥️ **Cursor & Screen Controls**: Full ANSI cursor positioning, visibility toggle, and screen/line clearing
- ❓ **Interactive Prompts**: Clean CLI inputs: `prompt()`, `confirm()`, `select()`, `password()`, `multi_select()`, and validated numbers
- 🔄 **100% Backward Compatible**: Direct drop-in replacement for the legacy `term_table` package (`cell()`, `box_top()`, `box_mid()`, `box_bot()`, `row()`, `badge()`)
- 🧪 **Comprehensive Test Suite**: 11 automated test suites with 100% pass rate

---

## 📁 Project Architecture

```
term/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade & convenience constructors
│   ├── types.alya          # Data structures & constants (ALIGN_*, BORDER_*, TreeNode)
│   ├── style.alya          # ANSI colors, text modifiers, visible_len, and badges
│   ├── border.alya         # Border style character presets
│   ├── table.alya          # Dynamic table formatter and builder engine
│   ├── box.alya            # Message callout banner generator
│   ├── tree.alya           # Unicode hierarchy tree renderer
│   ├── rule.alya           # Horizontal rules and aligned key-value pairs
│   ├── chart.alya          # Sparklines and horizontal percentage bar charts
│   ├── status.alya         # Status loggers and standard Unicode icons
│   ├── cursor.alya         # Terminal cursor movement and screen clearing
│   ├── live.alya           # In-place terminal line updating helpers
│   ├── progress.alya       # Progress bar and spinner indicators
│   ├── prompt.alya         # Interactive terminal prompts (ask, confirm, select, password)
│   └── compat.alya         # Drop-in compatibility layer with legacy term_table
├── examples/
│   └── demo.alya           # Runnable showcase demo
├── tests/                  # Automated test suites
│   ├── test_basic.alya
│   ├── test_box.alya
│   ├── test_chart.alya
│   ├── test_compat.alya
│   ├── test_cursor.alya
│   ├── test_progress.alya
│   ├── test_rule.alya
│   ├── test_status.alya
│   ├── test_style.alya
│   ├── test_table.alya
│   └── test_tree.alya
└── benches/
    └── bench_basic.alya    # Micro-benchmarks
```

> [!NOTE]
> **Modular Source Architecture:** Modules are structured cleanly inside `src/`. All public APIs are exported via `src/lib.alya` for clean, namespaced imports.

---

## 📦 Installation

Add `term` to the `[dependencies]` section in your `alya.toml`:

```toml
[dependencies]
term = { git = "https://github.com/alya-lang/term", branch = "main" }
```

Or install it directly using the Alya package CLI:

```bash
alyac add term --git https://github.com/alya-lang/term --branch main
alyac install
```

---

## 🚀 Quick Start

### 1. Dynamic Tables

```alya
import "term" as term

function main()
    let t = term::table(["ID", "Service", "Port", "Status"], [], "rounded")
    term::table_set_alignments(t, [term::ALIGN_LEFT, term::ALIGN_LEFT, term::ALIGN_RIGHT, term::ALIGN_CENTER])

    term::table_add_row(t, ["1", "API Gateway", "443", term::badge("RUNNING", "green")])
    term::table_add_row(t, ["2", "Database Pool", "5432", term::badge("DEGRADED", "yellow")])
    term::table_add_row(t, ["3", "Redis Cache", "6379", term::badge("STOPPED", "red")])

    term::table_print(t)
end

main()
```

### 2. Callout Banners

```alya
import "term" as term

function main()
    say term::box_info("Alya Compiler v0.0.16 is ready.", "RELEASE")
    say term::box_success("All test suites passed.", "DONE")
end

main()
```

### 3. Progress Indicators & Prompts

```alya
import "term" as term

function main()
    # Formatted progress bar
    say term::progress(75, 100, 25)

    # Interactive confirmation
    let proceed = term::confirm("Do you want to continue?", 1)
    if proceed
        say term::color_green("Proceeding...")
    end
end

main()
```

---

## 📖 API Reference

### Tables

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `table(headers, rows, border)` | `headers = [], rows = [], border = "unicode"` | `Table` | Creates a new Table builder with optional headers and rows. |
| `table_set_headers(tbl, headers)` | `tbl: Table, headers: array` | `Table` | Sets table header titles. |
| `table_add_row(tbl, row)` | `tbl: Table, row: array` | `Table` | Appends a single data row to the table. |
| `table_add_rows(tbl, rows)` | `tbl: Table, rows: array` | `Table` | Appends multiple data rows to the table. |
| `table_set_border(tbl, border_name)` | `tbl: Table, border_name: string` | `Table` | Changes border style preset (`"unicode"`, `"rounded"`, `"double"`, `"ascii"`, `"markdown"`, `"compact"`, `"none"`). |
| `table_set_alignments(tbl, aligns)` | `tbl: Table, aligns: array` | `Table` | Sets per-column alignment (`ALIGN_LEFT`, `ALIGN_CENTER`, `ALIGN_RIGHT`). |
| `table_set_max_widths(tbl, widths)` | `tbl: Table, widths: array` | `Table` | Sets per-column maximum character width (truncates overflow with `...`). |
| `table_render(tbl)` | `tbl: Table` | `string` | Renders the complete formatted table to string. |
| `table_print(tbl)` | `tbl: Table` | `void` | Prints the formatted table directly to stdout. |

### Callout Boxes

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `box_new(content, title, border)` | `content, title = "", border = "rounded"` | `CalloutBox` | Creates a new CalloutBox struct. |
| `box_render(cb)` | `cb: CalloutBox` | `string` | Renders a CalloutBox to string. |
| `box_info(content, title)` | `content, title = "INFO"` | `string` | Renders a cyan informational callout banner. |
| `box_success(content, title)` | `content, title = "SUCCESS"` | `string` | Renders a green success callout banner. |
| `box_warn(content, title)` | `content, title = "WARNING"` | `string` | Renders a yellow warning callout banner. |
| `box_error(content, title)` | `content, title = "ERROR"` | `string` | Renders a red error callout banner. |

### Progress Indicators

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `progress(current, total, width)` | `current, total, width = 30` | `string` | Formats a progress bar string (`[=======>  ] 70% (70/100)`). |
| `spinner(step, style)` | `step: int, style = "dots"` | `string` | Returns an animated spinner frame (`dots`, `line`, `pulse`, `arrows`, `blocks`). |

### Styling & Utility

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `color_<name>(text)` | `text: any` | `string` | Wraps text in 16-color ANSI code (`red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, `gray`). |
| `bg_<name>(text)` | `text: any` | `string` | Wraps text in ANSI background color. |
| `style_<name>(text)` | `text: any` | `string` | Wraps text in text modifier (`bold`, `dim`, `italic`, `underline`, `inverse`, `strikethrough`). |
| `color_rgb(text, r, g, b)` | `text: any, r: int, g: int, b: int` | `string` | Wraps text in 24-bit TrueColor RGB. |
| `color_256(text, code)` | `text: any, code: int` | `string` | Wraps text in 256-color palette code. |
| `strip_ansi(text)` | `text: any` | `string` | Strips all ANSI escape sequences from string. |
| `visible_len(text)` | `text: any` | `int` | Computes true printable character length ignoring ANSI codes. |
| `badge(text, color)` | `text: string, color = "green"` | `string` | Renders a styled colored badge. |
| `tag(label, val, color)` | `label: string, val: string, color = "cyan"` | `string` | Renders a `[label: val]` tag badge. |

### Tree Hierarchies

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `tree(root)` | `root: TreeNode` | `string` | Renders a hierarchical data tree with Unicode connectors (`├──`, `└──`, `│   `). |
| `node(label, children)` | `label: string, children = []` | `TreeNode` | Creates a parent tree node with nested children. |
| `leaf(label)` | `label: string` | `TreeNode` | Creates a terminal leaf node with no children. |

### Horizontal Rules & Key-Value Lists

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `hr(title, width, style)` | `title = "", width = 60, style = "single"` | `string` | Renders a horizontal divider rule (`"single"`, `"double"`, `"dashed"`, `"dotted"`, `"thick"`, `"wave"`). |
| `kv(key, value, width, leader)` | `key, value, width = 40, leader = "."` | `string` | Aligns key and value with dot leader characters. |
| `kv_list(pairs, width, leader)` | `pairs: array, width = 40, leader = "."` | `string` | Formats multiple key-value pairs into an aligned summary block. |

### Mini Visualizations

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `spark(values, min, max)` | `values: array, min = null, max = null` | `string` | Generates a compact inline sparkline graph using Unicode blocks (` ▂▃▄▅▆▇█`). |
| `bar(label, val, max, width)` | `label, val, max, width = 20` | `string` | Renders a horizontal percentage bar chart with labels and counters. |

### Status Loggers & Badges

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `success(msg)` / `log_success(msg)` | `msg: string` | `void` | Prints a green success message with checkmark icon (`✔ [SUCCESS]`). |
| `warn(msg)` / `log_warn(msg)` | `msg: string` | `void` | Prints a yellow warning message with warning icon (`⚠ [WARNING]`). |
| `error(msg)` / `log_error(msg)` | `msg: string` | `void` | Prints a red error message with cross icon (`✖ [ERROR]  `). |
| `info(msg)` / `log_info(msg)` | `msg: string` | `void` | Prints a cyan info message with info icon (`ℹ [INFO]   `). |
| `step(cur, tot, msg)` | `cur: int, tot: int, msg: string` | `void` | Prints a step counter with arrow icon (`➔ [1/3]`). |
| `format_<type>(msg)` | `msg: string` | `string` | String formatter variants returning styled strings instead of printing. |

### Cursor & Screen Controls

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `cursor_to(row, col)` | `row: int, col: int` | `string` | Returns ANSI escape sequence to place cursor at row and column. |
| `cursor_up(n)` / `cursor_down(n)` | `n = 1` | `string` | Moves cursor up or down by `n` lines. |
| `cursor_right(n)` / `cursor_left(n)` | `n = 1` | `string` | Moves cursor forward or backward by `n` columns. |
| `cursor_hide()` / `cursor_show()` | *(none)* | `string` | Toggles terminal cursor visibility. |
| `clear_screen()` | *(none)* | `string` | Clears entire screen and resets cursor to top-left home. |
| `clear_line()` | *(none)* | `string` | Clears entire current line and resets carriage to column 0. |

### Live In-Place Updates

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `live_start(initial_text)` | `initial_text = ""` | `void` | Initializes a live terminal line. |
| `live_update(text)` | `text: string` | `void` | Overwrites the previous terminal line in-place without scrolling. |
| `live_finish(text)` | `text: string` | `void` | Finalizes the live terminal line. |

### Interactive Prompts

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `prompt(msg, default_val)` | `msg: string, default_val = ""` | `string` | Prompts user for text input with optional default value. |
| `confirm(msg, default_yes)` | `msg: string, default_yes = 1` | `int` | Asks yes/no confirmation question (returns `1` or `0`). |
| `select(msg, options, default_idx)` | `msg: string, options: array, default_idx = 0` | `string` | Displays numbered selection menu and returns chosen option string. |
| `password(msg, mask)` | `msg: string, mask = "*"` | `string` | Secure password prompt using ANSI concealment and optional masking. |
| `multi_select(msg, opts, defaults)` | `msg: string, opts: array, defaults = []` | `array` | Checkbox-style multi-selection prompt with comma-separated inputs. |
| `prompt_required(msg, err_msg)` | `msg: string, err_msg = "..."` | `string` | Prompts continuously until non-empty input is received. |
| `prompt_number(msg, min, max, def)` | `msg: string, min = 0, max = MAX, def = 0` | `int` | Prompts continuously until valid integer in range is entered. |

---

## 🧪 Running Tests & Benchmarks

Run the test suite using `alyac`:

```bash
alyac test
```

Run individual test files:

```bash
alyac run tests/test_basic.alya
alyac run tests/test_table.alya
alyac run tests/test_style.alya
```

Run the benchmark suite:

```bash
alyac run benches/bench_basic.alya
```

Run the example demo:

```bash
alyac run examples/demo.alya
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository and clone it locally
2. Install dependencies:
   ```bash
   alyac install
   ```
3. Create your feature branch (`git checkout -b feature/my-feature`)
4. Verify tests and formatting before opening a PR:
   ```bash
   alyac test
   alyac fmt . --check
   ```
5. Commit your changes (`git commit -m "feat: add feature"`) and open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.