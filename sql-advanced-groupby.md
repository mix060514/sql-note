# SQL GROUP BY 高級用法

Origin data

|    | product_type   | product_name   |   product_count |
|---:|:---------------|:---------------|----------------:|
|  0 | shirt          | t-shirt        |               3 |
|  1 | shirt          | t-shirt        |               8 |
|  2 | shirt          | polo           |              25 |
|  3 | pants          | jeans          |               6 |


## ROLLUP
* order is important

```sql
WITH
  Products AS (
    SELECT 'shirt' AS product_type, 't-shirt' AS product_name, 3 AS product_count UNION ALL
    SELECT 'shirt', 't-shirt', 8 UNION ALL
    SELECT 'shirt', 'polo', 25 UNION ALL
    SELECT 'pants', 'jeans', 6
  )
SELECT product_type, product_name, sum(product_count)
FROM Products
group by rollup (product_type, product_name)
```
|    | product_type   | product_name   |   f0_ |
|---:|:---------------|:---------------|------:|
|  0 | nan            | nan            |    42 |
|  1 | shirt          | nan            |    36 |
|  2 | shirt          | t-shirt        |    11 |
|  3 | shirt          | polo           |    25 |
|  4 | pants          | nan            |     6 |
|  5 | pants          | jeans          |     6 |


```sql
WITH
  Products AS (
    SELECT 'shirt' AS product_type, 't-shirt' AS product_name, 3 AS product_count UNION ALL
    SELECT 'shirt', 't-shirt', 8 UNION ALL
    SELECT 'shirt', 'polo', 25 UNION ALL
    SELECT 'pants', 'jeans', 6
  )
SELECT product_type, product_name, sum(product_count)
FROM Products
group by rollup (product_name, product_type)
```


|    | product_type   | product_name   |   f0_ |
|---:|:---------------|:---------------|------:|
|  0 | nan            | nan            |    42 |
|  1 | nan            | t-shirt        |    11 |
|  2 | shirt          | t-shirt        |    11 |
|  3 | nan            | polo           |    25 |
|  4 | shirt          | polo           |    25 |
|  5 | nan            | jeans          |     6 |
|  6 | pants          | jeans          |     6 |

## CUBE

```sql
WITH
  Products AS (
    SELECT 'shirt' AS product_type, 't-shirt' AS product_name, 3 AS product_count UNION ALL
    SELECT 'shirt', 't-shirt', 8 UNION ALL
    SELECT 'shirt', 'polo', 25 UNION ALL
    SELECT 'pants', 'jeans', 6
  )
SELECT product_type, product_name, sum(product_count)
FROM Products
group by cube (product_name, product_type)
```


|    | product_type   | product_name   |   f0_ |
|---:|:---------------|:---------------|------:|
|  0 | nan            | nan            |    42 |
|  1 | nan            | t-shirt        |    11 |
|  2 | shirt          | nan            |    36 |
|  3 | shirt          | t-shirt        |    11 |
|  4 | nan            | polo           |    25 |
|  5 | shirt          | polo           |    25 |
|  6 | nan            | jeans          |     6 |
|  7 | pants          | nan            |     6 |
|  8 | pants          | jeans          |     6 |


## GROUPING SETS

```sql
-- GROUP BY with GROUPING SETS
WITH
  Products AS (
    SELECT 'shirt' AS product_type, 't-shirt' AS product_name, 3 AS product_count UNION ALL
    SELECT 'shirt', 't-shirt', 8 UNION ALL
    SELECT 'shirt', 'polo', 25 UNION ALL
    SELECT 'pants', 'jeans', 6
  )
SELECT product_type, product_name, sum(product_count)
FROM Products
group by grouping sets (product_type, product_name)
```
|    | product_type   | product_name   |   f0_ |
|---:|:---------------|:---------------|------:|
|  0 | shirt          | nan            |    36 |
|  1 | nan            | t-shirt        |    11 |
|  2 | nan            | polo           |    25 |
|  3 | pants          | nan            |     6 |
|  4 | nan            | jeans          |     6 |







```sql
WITH
  Products AS (
    SELECT 'shirt' AS product_type, 't-shirt' AS product_name, 3 AS product_count UNION ALL
    SELECT 'shirt', 't-shirt', 8 UNION ALL
    SELECT 'shirt', 'polo', 25 UNION ALL
    SELECT 'pants', 'jeans', 6
  )
SELECT product_type, product_name, sum(product_count)
FROM Products
group by grouping sets (product_type, (product_type, product_name))
```
|    | product_type   | product_name   |   f0_ |
|---:|:---------------|:---------------|------:|
|  0 | shirt          | nan            |    36 |
|  1 | shirt          | t-shirt        |    11 |
|  2 | shirt          | polo           |    25 |
|  3 | pants          | nan            |     6 |
|  4 | pants          | jeans          |     6 |



```sql
WITH
  Products AS (
    SELECT 'shirt' AS product_type, 't-shirt' AS product_name, 3 AS product_count UNION ALL
    SELECT 'shirt', 't-shirt', 8 UNION ALL
    SELECT 'shirt', 'polo', 25 UNION ALL
    SELECT 'pants', 'jeans', 6
  )
SELECT product_type, product_name, sum(product_count)
FROM Products
group by grouping sets ((), product_type, (product_type, product_name))
```

|    | product_type   | product_name   |   f0_ |
|---:|:---------------|:---------------|------:|
|  0 | nan            | nan            |    42 |
|  1 | shirt          | nan            |    36 |
|  2 | shirt          | t-shirt        |    11 |
|  3 | shirt          | polo           |    25 |
|  4 | pants          | nan            |     6 |
|  5 | pants          | jeans          |     6 |
