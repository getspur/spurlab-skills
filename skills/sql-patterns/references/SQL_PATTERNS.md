# SQL Patterns

DuckDB analytical pattern library for SpurLab SQL cells. Adapted from the MotherDuck agent-skills query playbook. The cell mechanics (insert, run, version conflicts) live in `live-query`.

## Contents

| Section | Covers |
|---|---|
| Posture | SQL-first, bounded reads, where filters live |
| Row selection | Latest per key, top-N per group, dedup |
| Aggregation | Conditional, running totals, period-over-period |
| Reshaping | PIVOT, UNPIVOT, UNION BY NAME |
| Joins | Grain checks, ASOF, avoiding row multiplication |
| Friendly SQL | FROM-first, GROUP BY ALL, EXCLUDE/REPLACE, alias reuse |
| Sampling | Big relations, approximate counts |
| Common mistakes | Failure patterns |

## Posture

- Keep grouping, filtering, and reshaping in SQL. Do not post-process results in the conversation.
- Filter early and aggregate early: put predicates inside the CTE that reads the relation.
- Filters on a table-function source belong in the TF arguments (`source(arg := …)`). Residual `WHERE` stays in DuckDB. Keep `tf_invocations * pages_per_tf` at or under 20.
- Bound exploration reads (`LIMIT`, `SUMMARIZE`, one TF page, `USING SAMPLE`). An unbounded read of a large relation stops for confirmation.
- A repeated shape becomes a named dataset through `promote-dataset` on the cell. Do not `CREATE TABLE AS`.

## Row selection

### Latest row per key

```sql
SELECT customer_id,
       max(order_date) AS latest_order_date,
       arg_max(amount, order_date) AS latest_amount
FROM orders
GROUP BY customer_id;
```

### Top N per group

```sql
SELECT category, product_name, revenue
FROM products
QUALIFY RANK() OVER (PARTITION BY category ORDER BY revenue DESC) <= 3;
```

### Deduplication

```sql
SELECT *
FROM raw_events
QUALIFY ROW_NUMBER() OVER (PARTITION BY event_id ORDER BY ingested_at DESC) = 1;
```

## Aggregation

### Conditional aggregation with FILTER

```sql
SELECT customer_id,
       COUNT(*) FILTER (WHERE status = 'completed') AS completed_orders,
       COUNT(*) FILTER (WHERE status = 'returned') AS returned_orders,
       SUM(amount) FILTER (WHERE status = 'completed') AS completed_revenue
FROM orders
GROUP BY customer_id;
```

### Running totals

```sql
WITH daily AS (
    SELECT order_date, SUM(amount) AS daily_revenue
    FROM orders
    GROUP BY ALL
)
SELECT order_date, daily_revenue,
       SUM(daily_revenue) OVER (ORDER BY order_date) AS cumulative_revenue
FROM daily;
```

### Year-over-year

```sql
WITH monthly AS (
    SELECT EXTRACT(YEAR FROM order_date) AS yr,
           EXTRACT(MONTH FROM order_date) AS mo,
           SUM(amount) AS revenue
    FROM orders
    GROUP BY ALL
)
SELECT curr.mo AS month,
       curr.revenue AS this_year,
       prev.revenue AS last_year,
       ROUND(100.0 * (curr.revenue - prev.revenue) / prev.revenue, 1) AS yoy_pct
FROM monthly curr
JOIN monthly prev ON curr.mo = prev.mo
WHERE curr.yr = 2025 AND prev.yr = 2024
ORDER BY curr.mo;
```

## Reshaping

### PIVOT

```sql
PIVOT sales ON quarter USING SUM(revenue) GROUP BY region;
```

### UNPIVOT

```sql
UNPIVOT quarterly_report ON Q1, Q2, Q3, Q4
INTO NAME quarter VALUE revenue;
```

### UNION BY NAME for drifted schemas

```sql
SELECT * FROM events_2024
UNION BY NAME
SELECT * FROM events_2025;
```

## Joins

- Check grain before joining: `COUNT(*)` and `COUNT(DISTINCT key)` on each side first. A row count that grows after the join means fan-out.
- Correlated subqueries and cartesian joins are mistakes; use a CTE, a window function, or `ASOF JOIN`.
- Nearest preceding event:

```sql
SELECT o.*, e.event_type
FROM orders o
ASOF JOIN events e
  ON o.customer_id = e.customer_id AND o.order_date >= e.event_ts;
```

## Friendly SQL

```sql
FROM orders WHERE status = 'completed' LIMIT 10;   -- FROM-first

SELECT category, region, SUM(sales) AS total_sales
FROM transactions GROUP BY ALL;                    -- GROUP BY ALL

SELECT * EXCLUDE (internal_id, raw_payload) FROM events;   -- EXCLUDE

SELECT * REPLACE (UPPER(name) AS name) FROM customers;     -- REPLACE

SELECT price * quantity AS total
FROM line_items WHERE total > 100;                 -- alias reuse
```

## Sampling

```sql
SELECT * FROM large_table USING SAMPLE 1 PERCENT (bernoulli);
SELECT approx_count(DISTINCT user_id) FROM large_table;
```

Sample for shape; `SUMMARIZE` for per-column stats; exact aggregates only when the relation is small or bounded by TF arguments.

## Common mistakes

- PostgreSQL-specific syntax where DuckDB differs (string functions, date arithmetic, casting).
- `WHERE` on a window-function result instead of `QUALIFY`.
- Unqualified table names once more than one connection is attached.
- Pushing TF-filterable predicates into `WHERE`, multiplying invocations.
- Fan-out joins that silently multiply measures; always re-check grain after joining.
- `ORDER BY` inside intermediate CTEs.
