 Here's a README for your DuckDB Oat application:

---

# DuckDB Oat — In-Browser SQL Analytics

A lightweight, elegant web-based SQL editor powered by [DuckDB-Wasm](https://duckdb.org/docs/api/wasm/overview.html). Run queries, explore data, and visualize results entirely in your browser — no backend required.

---

## Features

| Feature | Description |
|---------|-------------|
| **DuckDB-Wasm Engine** | Full SQL analytics engine running locally in your browser |
| **File Import** | Drag-and-drop or click to import CSV, CSV.GZ, JSON, Parquet, Excel |
| **Auto-Decompression** | Automatic Gzip decompression for `.csv.gz` files |
| **Smart Autocomplete** | Context-aware SQL suggestions (keywords, functions, tables, columns) |
| **Dual Layout** | Toggle between vertical (stacked) and horizontal (side-by-side) layouts |
| **Resizable Panels** | Drag to adjust editor/results split |
| **Quick Actions** | Preview, Count, Stats buttons for selected tables |
| **Export** | Copy results to clipboard or download as CSV |
| **Dark Mode** | Toggle between light and dark themes |
| **Persistent Settings** | Layout and theme preferences saved to localStorage |

---

## Quick Start

1. Open `duckdb_qwen.html` in any modern browser
2. Wait for "Initializing DuckDB..." to complete
3. Import data via:
   - **Drag & drop** files onto the drop zone
   - **Click Import** button in the header
4. Select a table from the sidebar to auto-populate queries
5. Press **Ctrl+Enter** or click **Run** to execute

---

## Supported File Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| CSV | `.csv` | Auto-detect with `header=true` |
| Gzipped CSV | `.csv.gz` | Auto-decompress, headers enabled |
| TSV/TXT | `.tsv`, `.txt` | Tab-delimited support |
| Parquet | `.parquet` | Columnar format |
| JSON | `.json`, `.jsonl`, `.ndjson` | Auto-detect schema |
| Excel | `.xlsx`, `.xls` | Basic support |

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl + Enter` | Execute query |
| `Tab` | Accept autocomplete suggestion |
| `↑ / ↓` | Navigate suggestions |
| `Esc` | Close suggestions / modals |

---

## SQL Features

DuckDB Oat supports the full [DuckDB SQL dialect](https://duckdb.org/docs/sql/introduction.html):

- **Standard SQL**: `SELECT`, `WHERE`, `GROUP BY`, `ORDER BY`, `JOIN`
- **Window Functions**: `ROW_NUMBER()`, `RANK()`, `LAG()`, `LEAD()`
- **Aggregates**: `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`
- **CTEs**: `WITH` clauses for complex queries
- **Advanced**: `PIVOT`, `UNPIVOT`, `MACRO`, custom functions

### Example Queries

```sql
-- Basic select
SELECT * FROM my_table LIMIT 100;

-- Aggregation
SELECT category, COUNT(*), AVG(price) 
FROM sales 
GROUP BY category;

-- Window functions
SELECT *, ROW_NUMBER() OVER (PARTITION BY category ORDER BY price DESC) as rank
FROM products;

-- Table summary
SUMMARIZE my_table;
```

---

## Configuration

Settings are automatically saved to `localStorage`:

| Setting | Key | Default |
|---------|-----|---------|
| Theme | `theme` | `light` |
| Layout | `layout` | `vertical` |
| First visit flag | `duckdb_visited` | — |

---

## Architecture

```
┌─────────────────────────────────────┐
│  UI Layer (Tailwind CSS + Vanilla JS) │
├─────────────────────────────────────┤
│  DuckDB-Wasm (WebAssembly + Worker)   │
├─────────────────────────────────────┤
│  File System (In-Memory)              │
│  - Registered file buffers            │
│  - Auto-decompression (Pako.js)       │
└─────────────────────────────────────┘
```

---

## Browser Requirements

- **Chrome/Edge** 90+
- **Firefox** 90+
- **Safari** 15+
- WebAssembly support required

---

## Customization

### Changing Default CSV Behavior

The CSV reader defaults to `header=true`. To modify:

```javascript
// In the file import section, change:
read_csv_auto('${finalFileName}', header=true)
// to:
read_csv_auto('${finalFileName}', header=false)  // No headers
// or:
read_csv_auto('${finalFileName}')  // Pure auto-detect
```

### Adding Custom Completions

Edit the `completions` object in the script section:

```javascript
completions.keywords.push({ name: 'PIVOT', type: 'keyword', desc: 'Pivot table' });
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| DuckDB fails to load | Check internet connection (CDN required), refresh page |
| CSV import fails | Verify file encoding (UTF-8), check delimiter consistency |
| Large files slow | DuckDB-Wasm has memory limits; consider Parquet for big data |
| Dark mode not persisting | Clear localStorage, or check browser privacy settings |

---

## Credits

- **DuckDB**: [duckdb.org](https://duckdb.org)
- **DuckDB-Wasm**: [github.com/duckdb/duckdb-wasm](https://github.com/duckdb/duckdb-wasm)
- **Pako.js**: Gzip decompression
- **Tailwind CSS**: Styling framework
- **Font Awesome**: Icons

---

## License

MIT — Free for personal and commercial use.
