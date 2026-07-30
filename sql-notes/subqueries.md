# Subqueries: a primer

A companion to [sql-patterns.md](sql-patterns.md). Subqueries were the thing I
kept half-understanding, so I wrote out the types until they stopped blurring
together. Worked examples first, self-test at the bottom.

Dialect is MySQL and SQLite.

---

## The one idea

A **subquery is just a query inside another query.** The inner one runs first, produces a result, and the outer query uses that result. That's it. The only thing to learn is the handful of *shapes* a subquery can take, based on **what it returns** and **where it sits**.

Mental habit: when you read the inner query, pause and ask what this hands back: one value, a list of values, or a whole table. That answer tells you how it plugs into the outer query.

---

## The 3 shapes by what they RETURN

### 1. Scalar subquery: returns ONE value

Plugs in anywhere a single value would go: a comparison, the SELECT list, etc.

```sql
-- 626 Exchange Seats: find the last seat number
case when id % 2 = 1 and id = (select max(id) from Seat) then id ... end
```
`(select max(id) from Seat)` returns a single number. You compare `id` against it. If a "scalar" subquery ever returns more than one row, MySQL errors, so these are for min/max/count/one-cell answers.

### 2. List (column) subquery: returns a COLUMN of values

Used with `IN` / `NOT IN`. The inner query returns one column; the outer checks membership.

```sql
-- 1978 Manager Left: keep employees whose manager is NOT a current employee
where manager_id not in (select employee_id from Employees)
```
`(select employee_id from Employees)` returns the list of everyone still here. `not in` keeps rows whose manager is missing from that list. **This is the "orphan / missing reference" pattern**: "X left" or "X was deleted" becomes "X's id `not in` the list of current ids."

**`NOT IN` and NULLs, a gotcha:** if the inner list contains a NULL, `NOT IN` can return no rows unexpectedly. When the column can be NULL, add `where col is not null` inside the subquery, or use `NOT EXISTS`.

### 3. Row (tuple) subquery: returns a (col, col) PAIR set

Match multiple columns at once against a set of pairs.

```sql
-- 1164 Product Price: pull the row matching each product's latest in-window date
where (product_id, change_date) in (
 select product_id, max(change_date) from Products
 where change_date <= '2019-08-16' group by product_id)
```
The inner query returns `(product_id, latest_date)` pairs; the outer keeps rows whose pair matches. Use this when "the max alone" isn't enough and you need **the rest of that row**.

---

## The other axis: WHERE the subquery sits

- **In `WHERE`** (most common): filter outer rows using the inner result. (1978, 1164)
- **In `SELECT`**: compute a value per row, often a scalar. (626's `max(id)`)
- **In `FROM`** (a "derived table"): treat the inner query's result as a table you select from again. Handy for "aggregate, then filter/rank the aggregates."
- **Scalar compared to a total**: `having count(distinct x) = (select count(*) from X)` ("has every X", also in sql-patterns.md).

---

## Correlated vs non-correlated

- **Non-correlated:** the inner query stands alone and never mentions the outer query. It runs once. (All the examples above.)
- **Correlated:** the inner query references a column from the outer row, so it re-runs for each outer row. Powerful but slower.
```sql
-- "employees earning more than their department's average"
where salary > (select avg(salary) from Employees e2 where e2.dept = e1.dept)
```
The inner query depends on `e1.dept`, so it recomputes per employee. Tell them apart by asking: *does the inner query mention the outer table's alias?* If yes, it's correlated.

---

## When you DON'T need a subquery

From error 3 in sql-patterns.md: don't reach for a subquery on a plain count or sum per group. That is just a `GROUP BY`. Subqueries earn their keep when you need:
- a value computed from a *different* scope (a global max, a per-group max, a total to compare against), or
- a membership test against another set (`IN` / `NOT IN`), or
- the *rest of a row* that owns a min/max.

Also worth remembering: **"the top one by X" is often NOT a subquery**. `order by X desc limit 1`, with tie-breakers as extra `order by` keys, is simpler. That's how 1341's two halves worked.

---

## Quick self-test (redo from a blank editor)

1. "Rows whose foreign id no longer exists" → which shape? (List / `NOT IN`.)
2. "Each group's max, plus the rest of that row" → which shape? (Row / tuple `IN`.)
3. "Compare each row to a single global number" → which shape? (Scalar.)
4. "Busiest user, tie broken by name" → subquery or `order by ... limit 1`? (The latter.)
