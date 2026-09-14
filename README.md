# term

[![CI](https://github.com/alya-lang/term/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/term/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/alya-lang/term?color=blue&label=License)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fterm%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fterm%2Fmain%2Falya.toml&query=%24.package.version&label=Version&color=brightgreen)](alya.toml)

Modern, zero-dependency terminal UI toolkit for the Alya language ecosystem: ANSI styling, Unicode tables, callout boxes, progress bars, and interactive prompts.

---

## 🌟 Features

- ⚡ **Lightweight & Fast**: Built for speed with minimal overhead (>25M ops/sec for ANSI processing)
- 📊 **Dynamic Tables**: Auto-sizing columns, custom alignment (Left, Center, Right), multi-line cell support, and maximum column width truncation
- 🎨 **7 Border Presets**: `unicode` (┌─┬─┐), `rounded` (╭─┬─╮), `double` (╔═╦═╗), `ascii` (+-++), `markdown` (|---|), `compact`, and `none`
- 🌈 **ANSI-Aware Formatting**: Accurately measures printable visual width (`visible_len`) using `strip_ansi()`, ensuring colored badges and styled text never misalign table columns
- 💬 **Callout Banners**: Message boxes with embedded titles and pre-styled helpers (`box_info`, `box_success`, `box_warn`, `box_error`)
- ⏳ **Progress & Spinners**: Customizable progress bars with percentage/counts and animated spinner frames (`dots`, `line`, `pulse`, `arrows`, `blocks`)
- ❓ **Interactive Prompts**: Clean CLI inputs: `prompt()`, `confirm()` (`[y/N]`), and numbered `select()`
- 🔄 **100% Backward Compatible**: Direct drop-in replacement for the legacy `term_table` package (`cell()`, `box_top()`, `box_mid()`, `box_bot()`, `row()`, `badge()`)
- 🧪 **Well Tested**: Comprehensive 6-suite automated test coverage with standard assertions

---

## 📁 Project Architecture

```
term/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade & convenience constructors
│   ├── types.alya          # Data structures & constants (ALIGN_*, BORDER_*)
│   ├── style.alya          # ANSI colors, text modifiers, visible_len, and badges
│   ├── border.alya         # Border style character presets
│   ├── table.alya          # Dynamic table formatter and builder engine
│   ├── box.alya            # Message callout banner generator
│   ├── progress.alya       # Progress bar and spinner indicators
│   ├── prompt.alya         # Interactive terminal prompts (ask, confirm, select)
│   └── compat.alya         # Drop-in compatibility layer with legacy term_table
├── examples/
│   └── demo.alya           # Runnable usage examples
├── tests/                  # Automated test suites
│   ├── test_basic.alya
│   ├── test_box.alya
│   ├── test_compat.alya
│   ├── test_progress.alya
│   ├── test_style.alya
│   └── test_table.alya
└── benches/
    └── bench_basic.alya    # Micro-benchmarks
```

> [!NOTE]
> **Modular Source Architecture:** Modules are structured cleanly inside `src/` (`src/table.alya`, `src/style.alya`, `src/box.alya`, `src/progress.alya`, `src/prompt.alya`, `src/compat.alya`). All public APIs are exported via `src/lib.alya` for clean, namespaced imports.

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

### Interactive Prompts

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `prompt(msg, default_val)` | `msg: string, default_val = ""` | `string` | Prompts user for text input with optional default value. |
| `confirm(msg, default_yes)` | `msg: string, default_yes = 1` | `int` | Asks yes/no confirmation question (returns `1` or `0`). |
| `select(msg, options, default_idx)` | `msg: string, options: array, default_idx = 0` | `string` | Displays numbered selection menu and returns chosen option string. |

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