# Coffee Shop SQL Database

A PostgreSQL project built around a fictional coffee shop chain. It includes a schema with sample data and a collection of queries that progress from basic `SELECT` statements to joins, subqueries, and window-style calculations.

## Project Files

| File | Description |
| --- | --- |
| `create_insert.sql` | Drops and creates all tables, defines the relationships between them, and inserts the sample data. |
| `queries.sql` | Example queries grouped by topic, from basic to more advanced. |

## Database Schema

The database has four tables.

### `employees`

| Column | Type | Notes |
| --- | --- | --- |
| `employee_id` | `INT` | Primary key |
| `first_name` | `VARCHAR(50)` | |
| `last_name` | `VARCHAR(50)` | |
| `email` | `VARCHAR(50)` | |
| `hire_date` | `DATE` | |
| `gender` | `VARCHAR(1)` | `M` or `F` |
| `salary` | `INT` | |
| `coffeeshop_id` | `INT` | Foreign key to `shops` |

### `shops`

| Column | Type | Notes |
| --- | --- | --- |
| `coffeeshop_id` | `INT` | Primary key |
| `coffeeshop_name` | `VARCHAR(50)` | |
| `city_id` | `INT` | Foreign key to `locations` |

### `locations`

| Column | Type | Notes |
| --- | --- | --- |
| `city_id` | `INT` | Primary key |
| `city` | `VARCHAR(50)` | |
| `country` | `VARCHAR(50)` | |

### `suppliers`

| Column | Type | Notes |
| --- | --- | --- |
| `coffeeshop_id` | `INT` | Part of the composite primary key, foreign key to `shops` |
| `supplier_name` | `VARCHAR(40)` | Part of the composite primary key |
| `coffee_type` | `VARCHAR(20)` | |

### Relationships

- `employees.coffeeshop_id` references `shops.coffeeshop_id` with `ON DELETE SET NULL`.
- `shops.city_id` references `locations.city_id` with `ON DELETE SET NULL`.
- `suppliers.coffeeshop_id` references `shops.coffeeshop_id` with `ON DELETE CASCADE`.

```
locations (1) ──< shops (1) ──< employees
                     │
                     └──< suppliers
```

## Sample Data

- 1,000 employees
- 5 coffee shops: Common Grounds, Early Rise, Ancient Bean, Urban Grind, Trembling Cup
- 3 locations: Los Angeles, New York, London
- 14 supplier records covering Arabica, Robusta, Liberica, and Excelsa coffee types

## Getting Started

### Requirements

- PostgreSQL

### Setup

1. Create a database and connect to it:

   ```bash
   createdb coffeeshop
   psql coffeeshop
   ```

2. Run the schema and data script:

   ```sql
   \i create_insert.sql
   ```

   The script drops any existing tables with the same names (using `DROP TABLE IF EXISTS ... CASCADE`), so it can be safely re-run to reset the database.

3. Run the queries from `queries.sql`, either all at once or one section at a time:

   ```sql
   \i queries.sql
   ```

## Topics Covered in `queries.sql`

1. **Basic selection**: `SELECT`, selecting all or specific columns
2. **Filtering**: `WHERE`, `AND`, `OR`
3. **Conditions**: `IN`, `NOT IN`, `IS NULL`, `BETWEEN`
4. **Sorting and uniqueness**: `ORDER BY`, `LIMIT`, `DISTINCT`, column aliases with `AS`
5. **Date functions**: `EXTRACT`
6. **String functions**: `UPPER`, `LOWER`, `LENGTH`, `TRIM`
7. **Concatenation and pattern matching**: `||`, boolean expressions, `LIKE` wildcards
8. **Text manipulation and nulls**: `SUBSTRING`, `POSITION`, `COALESCE`
9. **Aggregate functions**: `MIN`, `MAX`, `AVG`, `SUM`, `COUNT`, `ROUND`
10. **Grouping**: `GROUP BY` and `HAVING`
11. **Conditional logic**: `CASE`, `CASE` with `GROUP BY`, and transposing data with `CASE`
12. **Joins**: `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`
13. **Set operations**: `UNION` and `UNION ALL`
14. **Subqueries**: in the `FROM`, `SELECT`, and `WHERE` clauses
15. **Moving totals**: a 30-day moving total of pay using a correlated subquery

## Example Queries

Top 10 highest paid employees:

```sql
SELECT employee_id, first_name, last_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 10;
```

Employee count and salary statistics for each coffee shop:

```sql
SELECT
    coffeeshop_id,
    COUNT(*) AS employee_count,
    AVG(salary) AS avg_salary,
    MIN(salary) AS min_salary,
    MAX(salary) AS max_salary,
    SUM(salary) AS total_salary
FROM employees
GROUP BY coffeeshop_id;
```

Employees who work in coffee shops located in the United States:

```sql
SELECT *
FROM employees
WHERE coffeeshop_id IN (
    SELECT coffeeshop_id
    FROM shops
    WHERE city_id IN (
        SELECT city_id
        FROM locations
        WHERE country = 'United States'
    )
);
```
