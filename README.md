# 🗄️ SQL Studio

A fully client-side SQL query tool that runs entirely in the browser — no server, no installation, no data ever leaves your machine.

Built on [sql.js](https://sql-wasm.org/) (SQLite compiled to WebAssembly) with a dark-themed IDE-style interface.

---

## ✨ Features

### Import
- **CSV** — auto-detects delimiter, handles quoted fields and multi-line values
- **CSV.GZ** — decompresses gzip in-browser using the native `DecompressionStream` API, no library needed
- **TSV** — tab-separated files
- **JSON** — arrays or objects with an array property
- **Multi-file** — drop multiple files at once, each becomes its own table
- **Auto type inference** — samples column values and automatically assigns `INTEGER`, `REAL`, or `TEXT` — no more everything showing as `Utf8`
- **Handles comma-formatted numbers** — `1,000,000` parsed correctly as an integer

### SQL Editor
- **CodeMirror** editor with SQL syntax highlighting
- **Autocomplete** — keyword and table/column hints as you type
- **Multi-tab** — open multiple query tabs simultaneously
- **Keyboard shortcut** — `Ctrl+Enter` / `Cmd+Enter` to run
- **Multi-statement** — separate statements with `;`, all execute in sequence
- **Selection run** — highlight part of a query to run only that portion
- **Adjustable height** — drag the handle between editor and results

### Results
- **Paginated table** — 200 rows per page, sticky header, sortable columns
- **Column type display** — shows `INTEGER`, `REAL`, `TEXT`, `DATE` under each header
- **Row filter** — live filter across all columns with operator support:
  - Plain text: `NFO`, `NIFTY`
  - Numeric: `>=100`, `>0`, `<=500`, `<1000`, `=650`, `!=0`
- **Table / JSON toggle** — switch between tabular and raw JSON view
- **Export CSV** — downloads current (filtered) result set
- **Copy as TSV** — copies to clipboard for pasting into Excel / Sheets

### Sidebar
- **Tables panel** — lists all loaded tables with row counts; refreshes automatically after every import or `DROP`
- **Schema view** — click any table to expand its column names and types; click a column name to insert it into the editor
- **Quick actions** — Preview, Schema, Count, Drop per table
- **Filter tables** — search box to quickly find a table by name
- **History panel** — last 50 queries with status, row count, and execution time; click to reload into editor
- **Snippets panel** — one-click SQL templates: value counts, numeric summary, window functions, JOINs, and more

### UX
- **Resizable sidebar** — drag the divider to adjust width
- **Status indicator** — live dot shows Ready / Busy / Error state
- **Toast notifications** — non-blocking feedback for import success, errors
- **Zero data upload** — everything stays in your browser tab; closing the tab clears all data

---

## 🚀 Usage

### Option A — Open directly
Download `index.html` and open it in any modern browser. No web server needed.

```bash
# macOS
open index.html

# Linux
xdg-open index.html

# Windows
start index.html
```

### Option B — Serve locally
```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

### Option C — GitHub Pages
Push to a repo and enable GitHub Pages — it works as a static site with no build step.

---

## 📋 Quick Start

1. Open `index.html` in Chrome, Firefox, or Edge
2. Drop a CSV, CSV.GZ, TSV, or JSON file onto the sidebar import zone
3. The file is loaded as a SQLite table — the table name is derived from the filename
4. A `SELECT * FROM your_table LIMIT 100` query is auto-populated
5. Press `Ctrl+Enter` to run

---

## ⚙️ How It Works

| Component | Library | Notes |
|-----------|---------|-------|
| SQL engine | [sql.js 1.10](https://github.com/sql-js/sql.js) | SQLite compiled to WASM, runs on main thread |
| CSV parsing | [PapaParse 5.4](https://www.papaparse.com/) | Streaming, handles edge cases |
| GZ decompression | Native `DecompressionStream` | No library, built into modern browsers |
| SQL editor | [CodeMirror 5.65](https://codemirror.net/) | Syntax highlighting + autocomplete |

**No backend. No build tool. No npm. No framework.** A single HTML file with CDN dependencies.

### Large File Handling

For large files (100MB+), the import pipeline:
1. Reads raw bytes **eagerly** inside the file-picker event handler before any async work, preventing stale file handle errors
2. Decompresses `.gz` files in 4MB streaming chunks to avoid memory spikes
3. Infers column types from 500 evenly-spaced sample rows
4. Inserts rows in **batched transactions of 5,000 rows**, yielding to the browser between each batch to prevent UI freeze and browser watchdog timeouts

---

## 🔍 Filter Syntax

The result filter box (above the table) supports:

| Input | Behaviour |
|-------|-----------|
| `NIFTY` | Substring match across all columns |
| `=NFO` | Exact string match in any column |
| `!=MCX` | Exclude rows where any column equals MCX |
| `>=1000` | Any numeric column ≥ 1000 |
| `<500` | Any numeric column < 500 |
| `>0` | Any numeric column greater than zero |

---

## 🌐 Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome 80+ | ✅ Full |
| Firefox 113+ | ✅ Full |
| Edge 80+ | ✅ Full |
| Safari 16.4+ | ✅ Full (`DecompressionStream` required for .gz) |
| Safari < 16.4 | ⚠️ CSV/JSON work, .gz files not supported |

---

## 📁 File Structure

```
index.html   ← entire application, single file
README.md    ← this file
```

All dependencies are loaded from [cdnjs.cloudflare.com](https://cdnjs.cloudflare.com) at runtime.

---

## 🔒 Privacy

- **No data is ever sent to a server.** All processing happens in your browser tab.
- **No telemetry, no analytics, no cookies.**
- Closing or refreshing the tab clears all loaded tables and query history.
- The tool works fully offline once the page has loaded (CDN assets are cached by the browser).

---

## 📝 Limitations

- Data is **in-memory only** — tables do not persist across page reloads
- Maximum practical file size depends on available browser RAM (tested up to ~600MB CSV.GZ)
- SQLite dialect — DuckDB-specific functions (`read_csv_auto`, `SUMMARIZE`, etc.) are not available
- No support for Parquet files (SQLite has no native Parquet reader)

---

## 🛠️ Development

No build step required. Edit `index.html` directly.

To swap the SQL engine for a newer version of sql.js, update the CDN URL:
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.10.3/sql-wasm.js"></script>
```

---

## 📄 License

MIT — do whatever you want with it.
