# term

[![CI](https://github.com/alya-lang/term/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/term/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/alya-lang/term?color=blue&label=License)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fterm%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fterm%2Fmain%2Falya.toml&query=%24.package.version&label=Version&color=brightgreen)](alya.toml)

Modern, zero-dependency terminal UI toolkit for the Alya language ecosystem: ANSI styling, Unicode tables, callout boxes, progress bars, and interactive prompts.

---

## 🌟 Features

- 📊 **Dynamic Tables**: Auto-sizing columns, custom alignment (Left, Center, Right), multi-line cell support, and maximum column width truncation.
- 🎨 **7 Border Presets**: `unicode` (┌─┬─┐), `rounded` (╭─┬─╮), `double` (╔═╦═╗), `ascii` (+-++), `markdown` (|---|), `compact`, and `none`.
- 🌈 **ANSI-Aware Formatting**: Accurately measures printable visual width (`visible_len`) using `strip_ansi()`, ensuring colored badges and styled text never misalign table columns.
- 💬 **Callout Banners**: Message boxes with embedded titles and pre-styled helpers (`box_info`, `box_success`, `box_warn`, `box_error`).
- ⏳ **Progress & Spinners**: Customizable progress bars with percentage/counts and animated spinner frames (`dots`, `line`, `pulse`, `arrows`, `blocks`).
- ❓ **Interactive Prompts**: Clean CLI inputs: `prompt()`, `confirm()` (`[y/N]`), and numbered `select()`.
- 🔄 **100% Backward Compatible**: Direct drop-in replacement for the legacy `term_table` package (`cell()`, `box_top()`, `box_mid()`, `box_bot()`, `row()`, `badge()`).

---

## 📁 Project Architecture

```
term/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade & convenience constructors
│   ├── types.alya          # Core struct definitions & constants (ALIGN_*, BORDER_*)
│   ├── style.alya          # ANSI colors, text modifiers, visible_len, and badges
│   ├── border.alya         # Border style character presets
│   ├── table.alya          # Dynamic table formatter and builder engine
│   ├── box.alya            # Message callout banner generator
│   ├── progress.alya       # Progress bar and spinner indicators
│   ├── prompt.alya         # Interactive terminal prompts (ask, confirm, select)
│   └── compat.alya         # Drop-in compatibility layer with legacy term_table
├── examples/
│   └── demo.alya           # Comprehensive visual showcase
├── tests/                  # 6 test suites with 100% pass rate
│   ├── test_basic.alya
│   ├── test_box.alya
│   ├── test_compat.alya
│   ├── test_progress.alya
│   ├── test_style.alya
│   └── test_table.alya
└── benches/
    └── bench_basic.alya    # Micro-benchmarks (>25M ops/sec)
```

---

## 📦 Installation

Add `term` to your project's `alya.toml`:

```toml
[dependencies]
term = { git = "https://github.com/alya-lang/term", branch = "main" }
```

Or install it directly via the `alyac` CLI:

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

Output:
```text
╭────┬───────────────┬──────┬──────────╮
│ ID │ Service       │ Port │ Status   │
├────┼───────────────┼──────┼──────────┤
│ 1  │ API Gateway   │  443 │ RUNNING  │
│ 2  │ Database Pool │ 5432 │ DEGRADED │
│ 3  │ Redis Cache   │ 6379 │ STOPPED  │
╰────┴───────────────┴──────┴──────────╯
```

### 2. Callout Boxes & Banners

```alya
import "term" as term

function main()
    say term::box_info("Alya Compiler v0.0.16 is ready.", "RELEASE")
    say term::box_success("All test suites passed.", "DONE")
end

main()
```

### 3. Colors, Styles & Badges

```alya
import "term" as term

function main()
    say term::color_green("Success: ") + "File created."
    say term::style_bold("Notice: ") + term::color_gray("Cached object.")
    say term::tag("ENV", "production", "green")
end

main()
```

### 4. Progress Bars & Spinners

```alya
import "term" as term

function main()
    # Formatted progress bar: [=================>       ] 75% (75/100)
    say term::progress(75, 100, 25)
    
    # Animated spinner glyphs
    say term::spinner(0, "dots") + " Compiling source code..."
end

main()
```

---

## 📖 API Reference

### Tables
| Function | Description |
|:---|:---|
| `table(headers, rows, border)` | Create a new `Table` with optional headers and rows |
| `table_set_headers(t, headers)` | Set table header titles |
| `table_add_row(t, row)` | Append a single data row (array of values) |
| `table_add_rows(t, rows)` | Append multiple rows |
| `table_set_border(t, border_name)` | Change border style (`"unicode"`, `"rounded"`, `"double"`, `"ascii"`, `"markdown"`, `"compact"`, `"none"`) |
| `table_set_alignments(t, aligns)` | Set per-column alignment (`ALIGN_LEFT`, `ALIGN_CENTER`, `ALIGN_RIGHT`) |
| `table_set_max_widths(t, widths)` | Set per-column maximum character width (truncates with `...`) |
| `table_render(t)` | Return the formatted table string |
| `table_print(t)` | Print the formatted table string to stdout |

### Callout Boxes
| Function | Description |
|:---|:---|
| `box_new(content, title, border)` | Create a customizable `CalloutBox` |
| `box_render(b)` | Render a `CalloutBox` to string |
| `box_info(msg, title)` | Render a cyan info callout box |
| `box_success(msg, title)` | Render a green success callout box |
| `box_warn(msg, title)` | Render a yellow warning callout box |
| `box_error(msg, title)` | Render a red error callout box |

### Styling & Utility
| Function | Description |
|:---|:---|
| `color_<name>(text)` | Wrap text in 16-color ANSI code (`red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, `gray`) |
| `bg_<name>(text)` | Wrap text in ANSI background color |
| `style_<name>(text)` | Wrap text in text style (`bold`, `dim`, `italic`, `underline`, `inverse`, `strikethrough`) |
| `color_rgb(text, r, g, b)` | 24-bit TrueColor RGB text |
| `color_256(text, code)` | 256-color palette text |
| `strip_ansi(text)` | Strip all ANSI escape sequences from string |
| `visible_len(text)` | Compute true printable character length ignoring ANSI codes |
| `badge(text, color)` | Render a styled badge |
| `tag(label, val, color)` | Render a `[label: val]` badge |

### Interactive Prompts
| Function | Description |
|:---|:---|
| `prompt(msg, default_val)` | Prompt user for text input with optional default |
| `confirm(msg, default_yes)` | Ask yes/no question (returns `1` or `0`) |
| `select(msg, options, default_idx)` | Display numbered selection menu and return chosen option |

---

## 🧪 Running Tests & Benchmarks

```bash
# Run automated test suite
alyac test

# Run micro-benchmarking suite
alyac run benches/bench_basic.alya

# Run comprehensive demo
alyac run examples/demo.alya
```

---

## 🤝 Contributing

Contributions are welcome! Please open an issue or submit a pull request adhering to standard Alya coding style (`alyac fmt . --check`).

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).