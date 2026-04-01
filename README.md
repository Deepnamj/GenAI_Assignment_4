# Assignment 4 – File Handling in Python

A hands-on Jupyter Notebook covering core Python file I/O concepts: writing and reading files, appending data, computing statistics from file contents, user input with validation, file existence checks, and generating formatted reports.

---

## Prerequisites

- Python 3.x
- Jupyter Notebook or JupyterLab

Install Jupyter if needed:

```bash
pip install notebook
```

Launch the notebook:

```bash
jupyter notebook Assignment_4.ipynb
```

---

## How to Run

Run cells **in order from top to bottom** using **Shift + Enter** (or the ▶ Run button). Tasks 1–4 build on the same file (`sales_data.txt`), so they **must be run in sequence**. Tasks 5–7 are largely independent. Below is a task-by-task breakdown.

---

### Task 1 — Writing Sales Data to a File (Cell 2)

**Run:** Cell 2

**What it does:** Writes a list of sales figures to `sales_data.txt` in two formats — first as a single comma-separated line, then as one value per line (overwriting each time). Reopens and prints the file after each write to confirm the result.

**Key concepts:**
- **`open(file, "w")`** — opens a file for writing; creates it if it doesn't exist, and **overwrites** it if it does.
- **`with` statement (context manager)** — automatically closes the file when the block exits, even if an error occurs. This is the preferred pattern over manually calling `f.close()`.
- **`",".join(str(s) for s in sales)`** — uses a generator expression inside `join()` to build a comma-separated string without an intermediate list.

**Test cases to try:**

```python
sales = [1200, 450, 980, 1500, 3000]

# After comma-separated write, sales_data.txt contains:
# 1200,450,980,1500,3000

# After newline write, sales_data.txt contains:
# 1200
# 450
# 980
# 1500
# 3000
```

---

### Task 2 — Reading a File Three Ways (Cell 4)

**Run:** Cell 4 *(requires Cell 2 to have been run first)*

**What it does:** Opens `sales_data.txt` three times, each time using a different read method: `.read()` for the full file as a single string, `.readline()` for just the first line, and `.readlines()` for all lines as a list.

**Key concepts:**
- **`.read()`** — returns the entire file as one string, including newline characters.
- **`.readline()`** — reads only the next single line; the file pointer advances, so calling it again returns the second line.
- **`.readlines()`** — returns a list where each element is one line, with `\n` characters still attached. Useful when you need to index or iterate individual lines.

**Test cases to try:**

```python
# Given sales_data.txt with values 1200–3000 (one per line):

f.read()        # → "1200\n450\n980\n1500\n3000\n"
f.readline()    # → "1200\n"  (first line only)
f.readlines()   # → ['1200\n', '450\n', '980\n', '1500\n', '3000\n']
```

---

### Task 3 — Appending Data to a File (Cell 6)

**Run:** Cell 6 *(requires Cell 2 to have been run first)*

**What it does:** Appends three new sales figures (`[5000, 2500, 1700]`) to the existing `sales_data.txt` without overwriting it. Reopens the file to print all 8 values, then prints the total line count.

**Key concepts:**
- **`open(file, "a")`** — opens a file in append mode; new content is added at the end. The existing content is preserved, unlike `"w"` mode which truncates.
- **`len(f.readlines())`** — a concise way to count lines by reading all of them into a list and measuring its length.

**Test cases to try:**

```python
new_sales = [5000, 2500, 1700]

# After appending, sales_data.txt contains 8 lines:
# 1200, 450, 980, 1500, 3000, 5000, 2500, 1700

len(f.readlines())   # → 8
```

---

### Task 4 — Computing Statistics from File (Cell 8)

**Run:** Cell 8 *(requires Cells 2 and 3 to have been run first)*

**What it does:** Reads all sales values from `sales_data.txt`, strips whitespace and converts each line to `int`, then computes and prints four statistics: total, maximum, minimum, and average.

**Key concepts:**
- **List comprehension with `.strip()` and `int()`** — `[int(c.strip()) for c in content]` is a clean one-liner to parse a file's lines into usable numbers.
- **Built-in aggregation functions** — `sum()`, `max()`, `min()`, and `len()` operate directly on a list without needing manual loops.

**Test cases to try:**

```python
# With sales = [1200, 450, 980, 1500, 3000, 5000, 2500, 1700]:

sum(sales)              # → 16330
max(sales)              # → 5000
min(sales)              # → 450
sum(sales) / len(sales) # → 2041.25
```

---

### Task 5 — Writing User-Input Products to a File (Cell 10)

**Run:** Cell 10

**What it does:** Prompts the user to enter 3 product names and prices interactively, writes each as `Name | Price` to `products.txt`, then reads and prints the file line by line.

**Key concepts:**
- **`try/except ValueError`** — wraps `int(input(...))` to catch non-numeric price entries and print a friendly message instead of crashing. The product is still written even if the price input was invalid (uses the last valid `price` value), which is a known limitation to be aware of.
- **Iterating a file directly** — `for line in f:` reads one line at a time without loading the whole file into memory. Efficient for large files.
- **f-strings for formatted writing** — `f.write(f"{name} | {price}\n")` produces clean, readable file output.

**How to interact:**

```
Enter prdt name: Apple
Enter price: 120
Enter prdt name: Banana
Enter price: 45
Enter prdt name: Orange
Enter price: 75
```

**Expected file output (`products.txt`):**

```
Apple | 120
Banana | 45
Orange | 75
```

**Edge cases to test:**

- Enter letters for price (e.g. `abc`) → prints `"Invalid input. Please enter a no"` and continues.
- Enter a float like `9.99` for price → raises `ValueError` since `int()` doesn't accept decimals; use `float()` if decimals are needed.

---

### Task 6 — File Existence Check (Cell 12)

**Run:** Cell 6

**What it does:** Asks the user to type a filename, checks whether it exists on disk using `os.path.exists()`, and either reads and prints its contents or displays a not-found message.

**Key concepts:**
- **`import os` / `os.path.exists()`** — the `os` module provides operating-system utilities. `os.path.exists(path)` returns `True` if the file or directory exists, without needing to open it.
- **Defensive file access** — always checking existence before opening prevents an unhandled `FileNotFoundError` crash.

**How to interact:**

```
Enter filename to check: products.txt   → prints file contents
Enter filename to check: missing.txt    → prints "File not found. Please check the filename."
```

**Edge cases to test:**

- Type a filename with the wrong extension (e.g. `products.csv`) → prints not-found message.
- Type a filename with extra spaces (e.g. `" products.txt "`) → `os.path.exists()` treats spaces literally; will not match — consider using `.strip()` on the input for robustness.

---

### Task 7 — Discount Report Generator (Cell 14)

**Run:** Cell 7

**What it does:** Takes a discount percentage as input, applies it to a hardcoded dictionary of 5 products, writes a formatted report to `discount_report.txt` with original price and discounted price per item, then reads back and prints the report along with the total item count and average discounted price.

**Key concepts:**
- **Iterating a dictionary with `.items()`** — `for prdt, price in prices.items()` unpacks both the key and value in one step.
- **f-string format specifier `:.2f`** — `f"{disc_price:.2f}"` formats a float to exactly 2 decimal places in the output file.
- **Separating write and read** — the file is written once (inside the loop), then opened separately for reading. Note: the `open(..., "w")` is currently **inside** the outer `for price in prices` loop, which causes the file to be reopened and overwritten on each outer iteration — this is a bug to be aware of (the outer loop is redundant and should be removed).

**How to interact:**

```
Enter discount %: 10
```

**Expected file output (`discount_report.txt`):**

```
Mouse | 500 | 450.00
Keyboard | 800 | 720.00
Monitor | 7000 | 6300.00
Pendrive | 400 | 360.00
Camera | 5000 | 4500.00
```

**Expected console output:**

```
Total items: 5
Avg discounted price: 2466.0
```

**Edge cases to test:**

- Enter `0` for discount → discounted price equals original price.
- Enter `100` for discount → all discounted prices become `0.00`.
- Enter a non-integer (e.g. `10.5`) → raises `ValueError` since `int()` is used; change to `float()` to support decimal percentages.

---

## Key Concepts Summary

| Task | Concept |
|------|---------|
| 1 | `open(..., "w")`, `with` context manager, `join()` for formatted writing |
| 2 | `.read()` vs `.readline()` vs `.readlines()` — three file reading methods |
| 3 | `open(..., "a")` for appending without overwriting; counting lines |
| 4 | Parsing file lines into integers; `sum()`, `max()`, `min()` aggregation |
| 5 | User input with `try/except ValueError`; iterating file lines directly |
| 6 | `os.path.exists()` for defensive file access before opening |
| 7 | Dictionary iteration with `.items()`; `:.2f` formatting; report generation |
