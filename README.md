# READY FOR SQL

Small Python utility to turn values from CSV / Excel files into **SQL-safe, single-quoted values** and save them into a new CSV file.

Perfect for quickly building things like:

```sql
WHERE id IN ('123','456','789')
```

…starting from a spreadsheet or CSV.

---

## What it does

* Opens **CSV, XLS, or XLSX** files
* Trims cell values, escapes single quotes (`O'Brien → O''Brien`)
* Wraps each value in single quotes: `foo → 'foo'`
* Lets you choose how to handle **multi-column** data:

  * Flatten everything into one SQL list
  * **or** keep the original table layout and just quote each cell
* Skips completely empty rows and ignores blank cells when flattening
* Writes the result as a **new CSV file** next to your original file, without overwriting anything

---

## Requirements

* Python **3.10+** (uses modern type hints like `str | None`)
* Tkinter (usually bundled with Python; on some Linux distros you may need to install `python3-tk`)
* For Excel support:

  * `.xlsx`: `openpyxl`
  * `.xls`: `xlrd` (a version that still supports `.xls`, e.g. `xlrd<2.0`)

Install optional dependencies with:

```bash
pip install openpyxl "xlrd<2.0"
```

> If you only work with CSV files, you don’t need `openpyxl` or `xlrd`.

---

## Installation

Clone or download this repository, then (optionally) create a virtual environment:

```bash
git clone https://github.com/yourusername/your-repo-name.git
cd your-repo-name

# optional but recommended
python -m venv .venv
source .venv/bin/activate  # on Windows: .venv\Scripts\activate

pip install openpyxl "xlrd<2.0"
```

Rename the script if you like; in this README we’ll call it:

```text
ready_for_sql.py
```

---

## Usage

You can run it from the command line **or** just run it with no arguments to use a file picker.

### 1. Command line

```bash
python ready_for_sql.py path/to/your_file.csv
python ready_for_sql.py path/to/your_file.xlsx
python ready_for_sql.py path/to/your_file.xls
```

### 2. No arguments (GUI file picker)

```bash
python ready_for_sql.py
```

A file dialog will open so you can select a `.csv`, `.xls`, or `.xlsx` file.

If something goes wrong (missing file, unsupported type, etc.), a message box will appear; if GUI is not available, the message is printed to the console.

---

## Output files & modes

The script never overwrites your original file.
Instead, it creates a new CSV in the **same folder** as the input with a name like:

```text
READY FOR SQL [MODE] original_stem.csv
READY FOR SQL [MODE] original_stem (2).csv
READY FOR SQL [MODE] original_stem (3).csv
...
```

Where `MODE` is one of:

* `LIST1C` – single-column list
* `LISTMC` – multi-column list (flattened)
* `TABLE` – table layout preserved

### Single-column files (`LIST1C`)

If your file effectively has **one column**, the script:

* Collects all **non-empty** cells from that column
* Trims whitespace
* Escapes single quotes
* Wraps each value in single quotes
* Writes **one long CSV row** containing all those values

Example input (`ids.csv`):

```csv
123
456
789
```

Output (`READY FOR SQL [LIST1C] ids.csv`):

```csv
'123','456','789'
```

> Note: header rows are not treated specially; if your first row is a header, it will be quoted like any other value.

### Multi-column files

If the file has **more than one column**, you’ll see a prompt:

> This file has multiple columns.
>
> **Yes** = Flatten by columns into a single SQL list
> **No** = Preserve rows/columns and quote each cell
> **Cancel** = Abort

#### Option 1: Flatten into one list (`LISTMC`)

* Goes **column by column**, then down each column (column-major order)
* Skips empty / whitespace-only cells
* Produces one long row of SQL-quoted values

Example input:

```csv
first_name,last_name
Ada,Lovelace
Tim,O'Brien
```

Choosing **Yes** (flatten) → output (`READY FOR SQL [LISTMC] ...`):

```csv
'first_name','Ada','Tim','last_name','Lovelace','O''Brien'
```

#### Option 2: Preserve table layout (`TABLE`)

* Keeps the original row/column structure
* Quotes **every cell** in non-empty rows
* Completely empty rows stay empty rows
* Partially empty rows get `''` for empty cells

Same input:

```csv
first_name,last_name
Ada,Lovelace
Tim,O'Brien
```

Choosing **No** (preserve) → output (`READY FOR SQL [TABLE] ...`):

```csv
'first_name','last_name'
'Ada','Lovelace'
'Tim','O''Brien'
```

---

## How values are transformed

For each cell:

1. Leading/trailing whitespace is removed
2. If the whole value is already wrapped in single quotes (e.g. `'foo'`), the outer pair is removed
3. Internal single quotes are doubled for SQL:
   `O'Brien` → `O''Brien`
4. The final value is wrapped in single quotes:
   `foo` → `'foo'`

So:

* `  O'Brien  ` → `'O''Brien'`
* `'already_quoted'` → `'already_quoted'`
* empty strings:

  * in list modes (`LIST1C` / `LISTMC`): **skipped**
  * in table mode (`TABLE`): turned into `''` (for non-empty rows)

---

## Notes & limitations

* Only `.csv`, `.xls`, and `.xlsx` are supported; other extensions will raise an error.
* For `.xlsx` you must have `openpyxl` installed.
* For `.xls` you must have an `xlrd` version that still supports `.xls` files (e.g. `<2.0`).
* Numeric Excel cells are converted to strings before quoting.
* Headers are not treated specially; they will be quoted like ordinary data.

---

## Contributing

Feel free to:

* Add options for:

  * Skipping header rows
  * Custom delimiters
  * Choosing row-major vs column-major flattening
* Open issues or PRs for bugs and improvements
