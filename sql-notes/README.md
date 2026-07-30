# SQL notes

Study notes from teaching myself SQL. They live in public because writing for
someone else forces the explanations to hold up.

- **[sql-patterns.md](sql-patterns.md)** - the query shapes that keep recurring,
  a framework for working out which one you are looking at, and a running log of
  my own mistakes with the fix and the lesson for each.
- **[subqueries.md](subqueries.md)** - the three subquery shapes, where each
  one sits in a query, correlated versus not, and when you do not need one at all.

Both are pitched at roughly the SQLBolt-finished stage. Dialect is MySQL and
SQLite. Corrections are welcome.

**Where I am:** SQLBolt done, all 18 lessons. LeetCode SQL 50 at 38/50.
HackerRank SQL (Basic) is next.

---

## Log

### July 29

Cleaned both files up and published them. Rereading them end to end was its own
lesson. A lot of what I wrote early on is phrased like someone who had not quite
understood it yet, which is useful to notice and slightly embarrassing to read.

### July 16

Broke a plateau. I had been stuck at 28/50 for a week, working daily and getting
nowhere, then went 28 to 38 in one session.

Cleared the whole Advanced Select and Joins section: self-joins, `UNION`,
`IF`/`CASE` computed columns, the consecutive-numbers trick joining on `id + 1`,
running totals through an inequality join, and "value as of a date."

What actually changed was running my queries against a real SQLite instance
instead of reading solutions and nodding along. Passing and failing for real
turns out to be a completely different activity from following an explanation.

Then I started subqueries, immediately struggled, and stopped to write
subqueries.md before going any further. 3 of 7 done.

Also added a pre-submit checklist, because five of that session's failures were
not SQL problems at all. I skimmed the requirements. Dropped a `round()`, then a
`group by`, then an `order by`, all on the same question, across three
submissions.

### July 13

5 to 28 on SQL 50 in one long sitting. Somewhere around the fifteenth problem I
noticed I was solving the same handful of shapes over and over, so I started
sql-patterns.md, and the error log alongside it.

### July 10

Finished SQLBolt. Started LeetCode SQL 50 the same day.

### July 8

Switched the collision project's notebooks from Python to R, because R is where
I am stronger. Kept SQL in the project anyway, since practising it is half the
point.

Loaded both years, fixed the padding problem, and wrote `collisions.db`. 272,301
rows from 2019 and 198,745 from 2020, 471,046 total, matching the source file
counts exactly.

Then spent an unreasonable stretch of the evening fighting git over lock files.
The commit history has three redundant merge commits standing as a monument to
that.

### July 4

Pulled `NCD_2019.csv` and `NCD_2020.csv`. Thought about dropping 2020 so COVID
would not skew things, then decided against it. The year-over-year comparison is
the interesting part.

Found the bug that has taught me the most so far: the two years zero-pad some
columns differently. Nothing errors, nothing warns. Every `GROUP BY` touching
those columns is just quietly wrong.

### July 3

Started the collision project. The dataset my original plan pointed at returned
a 404, so the first real task was finding a replacement. Ruled out the BC Data
Catalogue road-safety stats (pre-aggregated, too narrow) and ICBC's crash
dashboard (Tableau Public, nothing to download), and landed on Transport
Canada's National Collision Database.
