# MySQL Date Functions

Scalar functions operate on **individual values** — they take one input and return one output **per row**.

## 🗓️ The Date Functions

- **`NOW()`** — returns the current date and time
- **`CURDATE()`** — returns the current date only
- **`CURTIME()`** — returns the current time only
- **`DATE_FORMAT(date, format)`** — formats a date value based on a specified format string
- **`DATEDIFF(date1, date2)`** — returns the number of days between two dates

## 🗃️ Sample Table: `orders`

| order_id | customer | order_date | delivery_date |
|----------|----------|------------|---------------|
| 1 | Alice | 2024-01-10 | 2024-01-15 |
| 2 | Bob | 2024-02-03 | 2024-02-10 |
| 3 | Carol | 2024-03-20 | 2024-03-28 |
| 4 | David | 2024-04-05 | 2024-04-12 |
| 5 | Eva | 2024-05-18 | 2024-05-25 |

## 💡 Examples

### NOW

```sql
SELECT NOW();
-- Result: 2024-05-26 14:35:22
-- Returns the current date AND time at the moment the query runs
```

### CURDATE

```sql
SELECT CURDATE();
-- Result: 2024-05-26
-- Returns today's date only, no time

SELECT customer, order_date
FROM orders
WHERE order_date = CURDATE();
-- Finds all orders placed today
```

### CURTIME

```sql
SELECT CURTIME();
-- Result: 14:35:22
-- Returns the current time only, no date
```

### DATE_FORMAT

```sql
SELECT customer, DATE_FORMAT(order_date, '%M %d, %Y') AS formatted_date
FROM orders;
-- Result:
-- Alice → January 10, 2024
-- Bob   → February 03, 2024
-- Carol → March 20, 2024

SELECT customer, DATE_FORMAT(order_date, '%d/%m/%Y') AS formatted_date
FROM orders;
-- Result:
-- Alice → 10/01/2024
-- Bob   → 03/02/2024
-- Carol → 20/03/2024
```

**Common format specifiers:**

| Specifier | Meaning | Example |
|-----------|---------|---------|
| `%Y` | 4-digit year | 2024 |
| `%y` | 2-digit year | 24 |
| `%M` | Full month name | January |
| `%m` | Month as number (01–12) | 01 |
| `%d` | Day of month (01–31) | 10 |
| `%D` | Day with suffix | 10th |
| `%H` | Hour (00–23) | 14 |
| `%i` | Minutes (00–59) | 35 |
| `%s` | Seconds (00–59) | 22 |

### DATEDIFF

```sql
SELECT customer,
       order_date,
       delivery_date,
       DATEDIFF(delivery_date, order_date) AS days_to_deliver
FROM orders;
-- Result:
-- Alice → 5 days
-- Bob   → 7 days
-- Carol → 8 days
-- David → 7 days
-- Eva   → 7 days
```

> `DATEDIFF(date1, date2)` subtracts **date2 from date1**. If date2 is later than date1, the result is negative.

### Combining functions

```sql
SELECT
    customer,
    DATE_FORMAT(order_date, '%M %d, %Y')      AS order_date,
    DATE_FORMAT(delivery_date, '%M %d, %Y')   AS delivery_date,
    DATEDIFF(delivery_date, order_date)        AS days_to_deliver
FROM orders;
```

## ⚠️ Things to Watch Out For

- **`NOW()` vs `CURDATE()`** — `NOW()` includes the time component; `CURDATE()` is date only. Using `NOW()` in a `WHERE` filter on a date column may not match as expected.
- **`DATEDIFF()` order matters** — `DATEDIFF(date1, date2)` gives a positive number if date1 is later, and a negative number if date2 is later.
- **`DATE_FORMAT()` is MySQL-specific** — other databases use different functions (e.g. `FORMAT()` in SQL Server, `TO_CHAR()` in PostgreSQL).

## 📌 Key Rules

- Date functions return **one value per row** just like other scalar functions.
- They can be used in `SELECT`, `WHERE`, and `ORDER BY`.
- `NOW()`, `CURDATE()`, and `CURTIME()` take **no arguments** — do not pass a column into them.
- Use `AS` to give computed columns readable names.

## 🔍 Using Date Functions in WHERE

```sql
SELECT customer, order_date
FROM orders
WHERE order_date = CURDATE();
-- Orders placed today

SELECT customer, order_date, delivery_date
FROM orders
WHERE DATEDIFF(delivery_date, order_date) > 7;
-- Orders that took more than 7 days to deliver
```

## 📋 Quick Reference

| Function | Syntax | What it returns |
|----------|--------|-----------------|
| `NOW` | `NOW()` | Current date and time |
| `CURDATE` | `CURDATE()` | Current date only |
| `CURTIME` | `CURTIME()` | Current time only |
| `DATE_FORMAT` | `DATE_FORMAT(date, format)` | Date as a formatted string |
| `DATEDIFF` | `DATEDIFF(date1, date2)` | Days between two dates |

---

# SQL Date Data Types

MySQL has dedicated data types for storing date and time values. Choosing the right one matters for storage efficiency and query accuracy.

## 🗂️ The Date Data Types

| Data Type | Format | Example | Stores |
|-----------|--------|---------|--------|
| `DATE` | `YYYY-MM-DD` | `2024-01-10` | Date only |
| `TIME` | `HH:MM:SS` | `14:35:22` | Time only |
| `DATETIME` | `YYYY-MM-DD HH:MM:SS` | `2024-01-10 14:35:22` | Date and time |
| `TIMESTAMP` | `YYYY-MM-DD HH:MM:SS` | `2024-01-10 14:35:22` | Date and time (UTC-aware) |
| `YEAR` | `YYYY` | `2024` | Year only |

## 💡 Examples

### DATE

```sql
CREATE TABLE orders (
    order_id   INT,
    customer   VARCHAR(50),
    order_date DATE
);

INSERT INTO orders VALUES (1, 'Alice', '2024-01-10');
```

### DATETIME

```sql
CREATE TABLE logs (
    log_id     INT,
    event      VARCHAR(100),
    logged_at  DATETIME
);

INSERT INTO logs VALUES (1, 'User login', '2024-01-10 09:15:00');
```

### TIMESTAMP

```sql
CREATE TABLE activity (
    activity_id  INT,
    action       VARCHAR(100),
    created_at   TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
-- created_at is automatically set to the current date and time on insert
```

## ⚠️ Things to Watch Out For

- **`DATETIME` vs `TIMESTAMP`** — both store date and time, but `TIMESTAMP` is stored in UTC and converts to the server's timezone on retrieval. `DATETIME` stores the value as-is with no timezone conversion.
- **`TIMESTAMP` range** — only supports dates from `1970-01-01` to `2038-01-19`. Use `DATETIME` for dates outside this range.
- **Always use quotes** — date values must be passed as strings in single quotes (e.g. `'2024-01-10'`), not as plain numbers.
- **Format must match** — MySQL expects dates in `YYYY-MM-DD` format. Inserting `'10-01-2024'` will not work correctly.

## 📌 Key Rules

- Always choose the **most specific type** — use `DATE` if you don't need the time, `TIME` if you don't need the date.
- `TIMESTAMP` is best for tracking **when a record was created or updated** because it can auto-populate with `DEFAULT CURRENT_TIMESTAMP`.
- Date values in queries must always be wrapped in **single quotes**.

---

# SQL Working with Dates

This section covers practical patterns for filtering, comparing, and extracting parts of dates in MySQL queries.

## 🗃️ Sample Table: `orders`

| order_id | customer | order_date | delivery_date |
|----------|----------|------------|---------------|
| 1 | Alice | 2024-01-10 | 2024-01-15 |
| 2 | Bob | 2024-02-03 | 2024-02-10 |
| 3 | Carol | 2024-03-20 | 2024-03-28 |
| 4 | David | 2024-04-05 | 2024-04-12 |
| 5 | Eva | 2024-05-18 | 2024-05-25 |

## 💡 Common Patterns

### Filtering by a specific date

```sql
SELECT * FROM orders
WHERE order_date = '2024-01-10';
-- Returns only Alice's order
```

### Filtering by a date range

```sql
SELECT * FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-03-31';
-- Returns orders from January to March 2024
-- Alice, Bob, and Carol qualify
```

### Filtering by month or year

```sql
SELECT * FROM orders
WHERE MONTH(order_date) = 2;
-- Returns orders placed in February → Bob

SELECT * FROM orders
WHERE YEAR(order_date) = 2024;
-- Returns all orders in 2024
```

### Extracting parts of a date

```sql
SELECT customer,
       DAY(order_date)   AS order_day,
       MONTH(order_date) AS order_month,
       YEAR(order_date)  AS order_year
FROM orders;
-- Result:
-- Alice → 10, 1, 2024
-- Bob   → 3,  2, 2024
-- Carol → 20, 3, 2024
```

### Comparing two date columns

```sql
SELECT customer,
       order_date,
       delivery_date,
       DATEDIFF(delivery_date, order_date) AS days_to_deliver
FROM orders
WHERE DATEDIFF(delivery_date, order_date) > 7;
-- Returns orders that took more than 7 days to deliver
-- Carol (8 days) qualifies
```

### Sorting by date

```sql
SELECT customer, order_date
FROM orders
ORDER BY order_date ASC;
-- Oldest orders first

SELECT customer, order_date
FROM orders
ORDER BY order_date DESC;
-- Most recent orders first
```

### Finding records before or after a date

```sql
SELECT customer, order_date
FROM orders
WHERE order_date < '2024-03-01';
-- Orders before March 2024 → Alice, Bob

SELECT customer, order_date
FROM orders
WHERE order_date >= '2024-04-01';
-- Orders from April onwards → David, Eva
```

## ⚠️ Things to Watch Out For

- **Always use `YYYY-MM-DD` format** — MySQL may misinterpret other formats silently.
- **`BETWEEN` is inclusive** — `BETWEEN '2024-01-01' AND '2024-03-31'` includes both January 1 and March 31.
- **Comparing `DATETIME` with `DATE`** — if a column is `DATETIME`, filtering with `WHERE col = '2024-01-10'` may miss rows because it compares against `'2024-01-10 00:00:00'` only. Use `DATE(col) = '2024-01-10'` to be safe.

## 📌 Key Rules

- Use `BETWEEN` for date ranges — it is cleaner and more readable than using `>=` and `<=` separately.
- Use `MONTH()`, `DAY()`, and `YEAR()` to extract specific parts of a date for filtering or display.
- Always `ORDER BY` a date column when the sequence of records matters.
- Wrap all date values in **single quotes** in your queries.
