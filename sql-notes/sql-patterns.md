# SQL patterns, framework, and error log

Notes I keep while working through LeetCode's SQL 50 and building the
[BC collision analysis](../project-3-bc-collision-analysis/) project. Two ideas drive the whole thing:

1. SQL problems repeat a small number of *shapes*. The work is recognizing
   which shape you are looking at, because the shape picks the tool.
2. Every mistake goes in the error log below, with the fix and the lesson,
   so I stop repeating it.

Written for my own use, but if you are learning SQL and the same things keep
tripping you up, the error log is probably the most useful part. Dialect is
MySQL and SQLite.

Progress: 38/50 on SQL 50. SQLBolt complete. HackerRank SQL (Basic) in progress.

---

## The thought-process framework

Run these questions in order on every problem:

1. **What is one row of my output?** Pin down the grain (one row per day? per customer? per product's first year?). This usually tells you the `GROUP BY`.
2. **What columns does that row need: raw, aggregated, or riding along with a min/max?**
   - Raw column from the table → select it / put it in `GROUP BY`.
   - A count/sum/avg/min/max → an aggregate.
   - A column that comes *with* a min/max row ("the price of the first year") → subquery pattern.
3. **Which rows even count?** → the `WHERE` (raw-row filters like date windows, status). If the filter needs a `count`/`sum`, it's `HAVING`, not `WHERE`.
4. **Match to a pattern** (catalog below).
5. **Sanity-check against the sample.** Two bug-catchers: did I need `DISTINCT`? and is any selected column neither aggregated nor grouped (the "Frankenstein row" trap)?

Steps 1 to 3 name the shape, and the shape picks the tool. "I understand it when shown but can't produce it cold" = recognition working toward recall, normal mid-curve stage, closed only by reps + redoing solved problems.

---

## Pattern catalog

**Count / total per group**
```sql
select group_col, count(*) / sum(x)
from t
group by group_col
```

**Total or count over only SOME rows (conditional aggregation)**
```sql
sum(if(cond, value, 0)) -- total over matching rows
sum(cond) -- count of matching rows (cond is 1/0)
count(if(cond, 1, null)) -- count of matching rows (count ignores nulls)
```

**Percentage of rows meeting a condition**
```sql
round(avg(cond) * 100, 2) -- cond is 1/0, so avg = fraction; *100 = percent
```

**Unique count**
```sql
count(distinct col)
```

**Need columns FROM the min/max row (first login, first order, top score, first year)**
```sql
select ...
from t
where (id, val) in (
 select id, min(val) -- or max(val)
 from t
 group by id
)
```
Use this whenever you need *more than the min/max itself*, meaning the rest of the row too. Returns whole, intact rows, and keeps multiple rows if several tie for the min/max.

**Filter groups by an aggregate (≥ N per group, appears once, etc.)**
```sql
select group_col
from t
group by group_col
having count(...) = / >= N
```

**Filter a set with HAVING, then aggregate over it (e.g. biggest number appearing once)**
```sql
select max(num) as num
from t
where num in (
 select num from t group by num having count(num) = 1
)
```
`max()` over an empty set returns `NULL` automatically (handles "report null if none" for free).

**"Bought all / has every X"**
```sql
having count(distinct x) = (select count(x) from x_table)
```
Scalar subquery returns the total; compare the customer's distinct count to it.

**Date handling**
```sql
date_format(d, '%Y-%m') -- reshape a date to a string, e.g. 2018-12
year(d) / month(d) -- pull one piece out as a number
date_add(d, interval 1 day) -- move a date; e.g. min(event_date) + 1 day
d between 'start' and 'end' -- inclusive both ends
d <= '2019-08-16' -- ALWAYS quote date literals (see error #13)
```

**Self-join: when you need a column that lives on a DIFFERENT row of the same table (a manager's name, a parent record, a previous row)**
```sql
select m.employee_id, m.name, count(*) as reports_count, round(avg(e.age)) as average_age
from Employees as e -- the "report" copy
join Employees as m -- the "manager" copy
 on e.reports_to = m.employee_id
group by m.employee_id
order by m.employee_id
```
Alias the table twice, one role per alias (`e` = report, `m` = manager). Aggregate over one copy, select identity columns from the other. Trigger: the row you're building needs facts that sit on a *different row* of the same table. (LeetCode 1731)

**`UNION`: output has TWO row-sources with different filter logic**
```sql
select employee_id, department_id -- source A: grouped test
from Employee
group by employee_id
having count(*) = 1
union -- stacks A on B, dedupes
select employee_id, department_id -- source B: raw-row filter
from Employee
where primary_flag = 'Y'
```
Both SELECTs must return the **same number of columns** in the same order. `UNION` removes duplicate rows; `UNION ALL` keeps them (and is faster when you don't care). Trigger: you want to write `... OR ...` but the two sides need **different tools**: one grouped, one a plain filter, or they come from different tables. That `OR` wants to be a `UNION`. If both sides are simple filters on the same table, a plain `WHERE a OR b` is enough without a `UNION`. (LeetCode 1789)

**`count(*)` vs `count(col)`**
```sql
count(*) -- number of ROWS in the group
count(col) -- number of rows where col IS NOT NULL (silently skips NULLs)
```
Equal when the column has no NULLs; they diverge only when `col` can be NULL. When the intent is "how many rows," prefer `count(*)` because it can't be tripped up by NULLs.

**Computed Yes/No (or any per-row label) column: `IF` / `CASE` in the SELECT**
```sql
select x, y, z,
 if(x+y>z and x+z>y and y+z>x, 'Yes', 'No') as triangle -- MySQL IF
from Triangle
-- portable form:
-- case when <cond> then 'Yes' else 'No' end as triangle
```
A column that isn't in the table gets **built in the SELECT list**, not selected bare and not filtered in WHERE. `WHERE` removes rows; a label column keeps every row and just adds a value. Alias it with `as`. (LeetCode 610)

**Consecutive / streak / "N in a row": self-join on `id + 1`**
```sql
select distinct l1.num as ConsecutiveNums
from Logs l1
join Logs l2 on l2.id = l1.id + 1 -- the next row
join Logs l3 on l3.id = l2.id + 1 -- the row after that
where l1.num = l2.num and l2.num = l3.num
```
Bring the table in once per row you must compare at the same time, and line them up with arithmetic in `ON`. `ON` can hold expressions (`l1.id + 1`), not just `a=b`. Caveat: `id + 1` means "row whose id literally equals this+1," so it assumes gapless ids; with gaps use ranking functions instead. (LeetCode 180)

**Running total: inequality self-join, then `sum`**
```sql
select q1.person_name
from Queue q1
join Queue q2 on q2.turn <= q1.turn -- everyone up to and including q1
group by q1.person_id, q1.person_name, q1.turn
having sum(q2.weight) <= 1000
order by q1.turn desc
limit 1
```
Equality joins match one row; **inequality joins (`<=`/`>=`) match a whole range**, which is what lets you accumulate. Each `q1` gets a "stack" of every earlier row; `sum` totals the stack = running total. (LeetCode 1204)

**"Value as of a date": max row within a window, then `UNION` the untouched defaults**
```sql
select product_id, new_price as price
from Products
where (product_id, change_date) in (
 select product_id, max(change_date) from Products
 where change_date <= '2019-08-16' group by product_id) -- latest change in window
union
select product_id, 10 as price -- default for never-changed
from Products
where product_id not in (
 select product_id from Products where change_date <= '2019-08-16')
```
Combines two known patterns: the columns-from-the-max-row subquery (latest state ≤ the date) and `UNION` for rows that have no in-window record and fall back to a default. (LeetCode 1164)

---

## Error log

Review this before each session. Every entry is a mistake I actually made.

1. **`count()` inside `WHERE`** → "Invalid use of group function." Fix: move the count condition to `HAVING`. **Lesson:** `WHERE` filters raw rows *before* grouping (no aggregates); `HAVING` filters *after* grouping (aggregates allowed).

2. **`GROUP BY product_id` while selecting ungrouped `quantity`/`price`** → passed on LeetCode but is wrong. The engine grabs those columns from an *arbitrary* row, so the year can come from one row and the price from another ("Frankenstein row"), and it collapses multiple valid rows into one. Fix: the min/max-row subquery pattern. **Lesson:** if a selected column is neither aggregated nor in `GROUP BY`, that's a red flag. `MIN`/`MAX` only fixes its own column; the rest can drift.

3. **Over-applying the subquery pattern** (dangling `where (id, follower_id)` on a plain count-per-group) → syntax error / meaningless clause. Fix: delete it; simple counts don't need a subquery. **Lesson:** only reach for the subquery when you need columns *from* a min/max row. Plain count/sum per group = just `GROUP BY`.

4. **Invented syntax** (`where max(num) is distinct`) → syntax error. Fix: express each half of the intent in real SQL. "Appears once" = `group by ... having count = 1`; "largest" = `max(...)`. **Lesson:** say each half of the intent in a real construct; don't stack adjectives into one made-up line.

5. **Conditional counting with `COUNT`** (`count(state = 'approved')`) counts *all* rows, since the 0/1 result is always non-null. Fix: `sum(state = 'approved')`. **Lesson:** to count matching rows use `SUM(condition)` (or `COUNT(IF(cond,1,NULL))`), not `COUNT(condition)`.

6. **Alias with a space** (`... as approved_total amount`) → syntax error near the second word. Fix: `approved_total_amount`. **Lesson:** aliases can't contain unquoted spaces.

7. **`GROUP BY month and country`** → treated as one boolean expression. Fix: `GROUP BY month, country` (comma, not `and`).

8. **Wrong table name** (`from transaction` vs `Transactions`) → "table doesn't exist." **Lesson:** match the exact table name from the schema.

9. **`count(user_id)` where the same user has multiple rows/day** → overcounts. Fix: `count(distinct user_id)`. **Lesson:** "active users / unique X" almost always wants `DISTINCT`.

10. **Wrong grain, grouped by the obvious id instead of the real one** (1731: `group by employee_id` when one output row = one *manager*). Output had 4 rows; expected 1. Fix: group by `reports_to`/the manager, use a self-join for the manager's name. **Lesson:** run framework step 1 *in English* before typing: what is one row of my output? A row-count mismatch vs the sample (4 vs 1) is the tell.

11. **Skimmed stated requirements, dropped `round()`, then `group by`, then `order by` across three submissions of one problem.** Each is spelled out in the prompt ("rounded to nearest integer," "ordered by employee_id"). **Lesson:** mechanics were fine; *reading* was the bug. Run the pre-submit checklist below every time.

12. **Raw column in `HAVING` with no `GROUP BY`** (1789: `having count(...) = 1 or primary_flag = 'Y'`) → "Unknown column 'primary_flag' in 'having clause'." Two errors: no `GROUP BY`, and a raw-row test (`primary_flag`) shoved into `HAVING`. Fix: split the two conditions, putting the grouped test in `HAVING` and the raw filter in `WHERE`, then `UNION` them. **Lesson:** a group-level test and a raw-row test can't share one clause; if you're `OR`-ing them, reach for `UNION`.

13. **Unquoted date literal** (`change_date <= 2019-08-16`) → SQL reads it as arithmetic (2019 - 8 - 16 = 1995), not a date. Fix: quote it as `change_date <= '2019-08-16'`. **Lesson:** every date literal goes in single quotes, format `'YYYY-MM-DD'`.

14. **Building a new label column in `WHERE` instead of `SELECT`** (610: tried `select x,y,z,triangle ... where if(..., triangle='Yes', ...)`) → "Unknown column 'triangle'." A column that isn't in the table must be *built* with `IF`/`CASE` in the SELECT list; `WHERE` only filters rows. **Lesson:** "add a Yes/No column" = compute it in SELECT, not filter it in WHERE.

---

## Pre-submit checklist

My mechanics are usually fine. What I skim is the requirements. Ten-second pass before hitting Submit:

1. **Grain named?** Did I say, in English, what one output row is, and does my row count roughly match the sample?
2. **Every stated requirement present?** Scan the prompt for: `round` / decimal places, `order by`, `distinct`, "any order" vs specific order, exact output column names.
3. **Aggregates grouped?** Every `count`/`sum`/`avg` has a matching `GROUP BY` (or is a whole-table aggregate on purpose).
4. **No Frankenstein row?** Every selected column is aggregated, in `GROUP BY`, or genuinely one-per-group.

---

## Improvement habits

- **Plan in English first**: write a comment naming the shape (`-- one row per user, count followers, order by id`) before typing SQL.
- **Struggle ~10 min before looking**: the stuck feeling is the rep; ask for a hint, not the solution.
- **Redo solved problems from a blank editor after a few days**: passing once is recognition; redoing is recall (what interviews test).
- **Keep this error log current**: add each new mistake as it happens.
- **Say the query out loud**: the vague spot is the part not yet understood.
