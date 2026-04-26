# SQL Aggregate Functions

Aggregate functions take **multiple rows of data** and return a **single summary value**.

## 🔢 The 5 Core Functions

- **`COUNT()`** — counts the number of rows or non-NULL values in a column
- **`SUM()`** — adds up all values in a numeric column
- **`AVG()`** — returns the arithmetic mean of a numeric column
- **`MIN()`** — returns the smallest value (works on numbers, dates, and text)
- **`MAX()`** — returns the largest value (works on numbers, dates, and text)

## 🗃️ Sample Table: `orders`

| order_id | customer | amount | order_date |
|----------|----------|--------|------------|
| 1 | Alice | 500 | 2024-01-10 |
| 2 | Bob | 300 | 2024-01-15 |
| 3 | Alice | 150 | 2024-02-01 |
| 4 | Carol | NULL | 2024-02-10 |
| 5 | Bob | 800 | 2024-03-05 |

## 💡 Examples

### COUNT

```sql
SELECT COUNT(*) FROM orders;
-- Result: 5 (counts all rows, including the NULL one)

SELECT COUNT(amount) FROM orders;
-- Result: 4 (skips the NULL in row 4)
```

### SUM

```sql
SELECT SUM(amount) FROM orders;
-- Result: 1750 (500 + 300 + 150 + 800; NULL is ignored)
```

### AVG

```sql
SELECT AVG(amount) FROM orders;
-- Result: 437.50 (1750 ÷ 4 non-NULL rows, not 5)
```

### MIN and MAX

```sql
SELECT MIN(amount), MAX(amount) FROM orders;
-- Result: 150 | 800
```

### All five in one query

```sql
SELECT
    COUNT(*)    AS total_orders,
    SUM(amount) AS total_revenue,
    AVG(amount) AS avg_order,
    MIN(amount) AS smallest,
    MAX(amount) AS largest
FROM orders;
```

## ⚠️ The NULL Rule

- `SUM`, `AVG`, `MIN`, and `MAX` all **ignore NULL values** automatically.
- `COUNT(*)` **includes NULL rows** because it counts rows, not values.
- `COUNT(col)` **skips NULLs** because it counts values in a specific column.
- **AVG trap:** since NULLs are excluded, `AVG(col)` divides by the number of non-NULL rows — not the total row count. This can produce a higher average than expected when many rows have NULLs.

## 📌 Key Rules

- An aggregate always returns **exactly one row**.
- You can use multiple aggregate functions in a single `SELECT` — they all run on the full table.
- Use `AS` to give results readable column names.
- You **cannot mix** a regular column and an aggregate in the same `SELECT`. For example, `SELECT customer, COUNT(*)` is invalid on its own.
- Aggregates work with `WHERE` — the filter runs first, then the function summarizes the remaining rows.

## 🔍 Using WHERE with Aggregates

```sql
SELECT COUNT(*), SUM(amount)
FROM orders
WHERE amount > 200;
-- Rows 1, 2, and 5 qualify → COUNT = 3, SUM = 1600
```

> **Order of operations:** `WHERE` filters rows first, then the aggregate runs on whatever rows pass the filter.

## 📋 Quick Reference

| Function | Returns | NULL behavior |
|----------|---------|---------------|
| `COUNT(*)` | Total number of rows | Includes NULL rows |
| `COUNT(col)` | Rows where col is not NULL | Skips NULLs |
| `SUM(col)` | Total of all values | Ignores NULLs |
| `AVG(col)` | Mean of non-NULL values | Divides by non-NULL count |
| `MIN(col)` | Smallest value | Ignores NULLs |
| `MAX(col)` | Largest value | Ignores NULLs |
