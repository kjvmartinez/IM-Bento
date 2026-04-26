# SQL Scalar String Functions

Scalar functions operate on **individual values** — they take one input and return one output **per row**.

## 🔡 The String Functions

- **`UPPER(col)`** — converts all characters to uppercase
- **`LOWER(col)`** — converts all characters to lowercase
- **`LENGTH(col)`** — returns the number of characters in a string
- **`SUBSTRING(col, start, length)`** — extracts a portion of a string
- **`CONCAT(col1, col2, ...)`** — joins two or more strings together

## 🗃️ Sample Table: `employees`

| emp_id | first_name | last_name | email |
|--------|------------|-----------|-------|
| 1 | Alice | Santos | alice.santos@email.com |
| 2 | bob | reyes | bob.reyes@email.com |
| 3 | CAROL | LIM | carol.lim@email.com |
| 4 | David | Cruz | david.cruz@email.com |
| 5 | Eva | tan | eva.tan@email.com |

## 💡 Examples

### UPPER and LOWER

```sql
SELECT first_name, UPPER(first_name) AS upper_name
FROM employees;
-- Result:
-- Alice → ALICE
-- bob   → BOB
-- CAROL → CAROL

SELECT last_name, LOWER(last_name) AS lower_name
FROM employees;
-- Result:
-- Santos → santos
-- reyes  → reyes
-- LIM    → lim
```

### LENGTH

```sql
SELECT first_name, LENGTH(first_name) AS name_length
FROM employees;
-- Result:
-- Alice → 5
-- bob   → 3
-- CAROL → 5
-- David → 5
-- Eva   → 3
```

### SUBSTRING

```sql
SELECT email, SUBSTRING(email, 1, 5) AS first_five
FROM employees;
-- Result:
-- alice.santos@email.com → alice
-- bob.reyes@email.com    → bob.r
-- carol.lim@email.com    → carol
```

> `SUBSTRING(col, start, length)` — start position begins at **1**, not 0.

### CONCAT

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM employees;
-- Result:
-- Alice Santos
-- bob reyes
-- CAROL LIM
-- David Cruz
-- Eva tan
```

### Combining functions

```sql
SELECT
    CONCAT(UPPER(first_name), ' ', UPPER(last_name)) AS full_name,
    LENGTH(CONCAT(first_name, last_name))               AS name_length,
    LOWER(email)                                      AS email
FROM employees;
-- Scalar functions can be nested inside each other
```

## ⚠️ Things to Watch Out For

- **LENGTH and spaces** — `LENGTH` counts all characters including spaces, unlike `LEN` in SQL Server.
- **SUBSTRING start index** — starts at `1`, not `0`. Using `0` may return unexpected results.
- **CONCAT with NULL** — in some databases, concatenating with a NULL produces NULL. Use `COALESCE` or `ISNULL` to handle NULLs before concatenating.

## 📌 Key Rules

- Scalar functions return **one value per row** — the result table has the same number of rows as the input.
- They can be used in `SELECT`, `WHERE`, and `ORDER BY`.
- Scalar functions can be **nested** inside each other (e.g. `UPPER(SUBSTRING(col, 1, 3))`).
- They do **not** change the actual data in the table — only the output of the query.
- Use `AS` to give the computed column a readable name.

## 🔍 Using Scalar Functions in WHERE

```sql
SELECT first_name, last_name
FROM employees
WHERE UPPER(first_name) = 'ALICE';
-- Finds 'Alice', 'ALICE', 'alice' — case-insensitive search

SELECT first_name, email
FROM employees
WHERE LENGTH(first_name) > 3;
-- Returns employees whose first name is longer than 3 characters
```

## 📋 Quick Reference

| Function | Syntax | What it does |
|----------|--------|--------------|
| `UPPER` | `UPPER(col)` | Converts to uppercase |
| `LOWER` | `LOWER(col)` | Converts to lowercase |
| `LENGTH` | `LENGTH(col)` | Returns character count |
| `SUBSTRING` | `SUBSTRING(col, start, length)` | Extracts part of a string |
| `CONCAT` | `CONCAT(col1, col2, ...)` | Joins strings together |
