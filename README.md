# Excel_Columns_to_SQL_Literals
A python program which, on run, has the user select an Excel file, on selection it converts to SQL string literals &lt;'value'>, inserts commas between, and saved a csv next to the selected file. Also offers Command line execution.

Built with Python 3 and the standard library (`tkinter`, `pathlib`, `csv`, `sys`).


# READY FOR SQL

A tiny Python tool that takes a CSV file and produces a new CSV where every value
is wrapped and escaped for use in SQL (e.g. building `IN ('a','b','c')` lists).

## Features

- Accepts a CSV file via:
  - Command-line argument (`python ready_for_sql.py input.csv`)
  - File picker dialog if no argument is provided
- Detects single-column CSVs and flattens them into a single SQL-ready row
- Escapes single quotes for SQL (`O'Brien` → `'O''Brien'`)
- Writes output as: `READY FOR SQL <original-filename>.csv`

## Requirements

- Python 3.10+ (you’re using `|` in type hints)
- `tkinter` (usually included with standard Python installs)

## Usage

```bash
python ready_for_sql.py input.csv
# or just:
python ready_for_sql.py
# and choose the file from the dialog
