# Column Mapping

Fields in a view object are not only used to map columns from different tables but also to represent aggregate
expressions and grouping clauses.

## Aggregate Column Mapping

Applying aggregate functions is a core feature of aggregate queries. All data queried through aggregate columns must be
stored in the fields defined in the view class. Therefore, we need to map field names to aggregate columns, and using
prefix mapping is an excellent approach.

| Prefix     | Aggregate Function Name | Field Name      | Aggregate Column Expression            |
|------------|-------------------------|-----------------|----------------------------------------|
| sum        | sum                     | sumScore        | sum(score) AS sumScore                 |
| max        | max                     | maxScore        | max(score) AS maxScore                 |
| min        | min                     | minScore        | min(score) AS minScore                 |
| avg        | avg                     | avgScore        | avg(score) AS avgScore                 |
| first      | first                   | firstScore      | first(score) AS firstScore             |
| last       | last                    | lastScore       | last(score) AS lastScore               |
| stdDevPop  | stddev\_pop             | stdDevPopScore  | stddev\_pop(score) AS stdDevPopScore   |
| stdDevSamp | stddev\_samp            | stdDevSampScore | stddev\_samp(score) AS stdDevSampScore |
| stdDev     | stddev                  | stdDev          | stddev(score) AS stdDev                |
| addToSet   | addToSet                | addToSetScore   | addToSet(score) AS addToSetScore       |
| push       | push                    | pushScore       | push(score) AS pushScore               |
| count      | count                   | countScore      | count(score) AS countScore             |
| count      | count                   | count           | count(\*) AS count                     |
**Example**  

If you want to calculate the average value of a column named `score`, you can use the `avg` function. Define the field name as `avgScore`, following the naming convention of "aggregate prefix + column name". It will finally be mapped to `avg(score) AS avgScore`.

## Aggregate Expression Mapping  

For aggregate expressions that cannot be directly represented by a field name, you can specify the specific expression through annotations, mapping the expression to a column name, with the field name used as a label.  

**Example**  

Add an expression annotation when defining a field:  

```java
@Column(name = "sum(l_extendedprice*(1-l_discount))")
private BigDecimal sum_disc_price;
```

This field is mapped to:  

```sql
sum(l_extendedprice*(1-l_discount)) AS sum_disc_price
```

**Complete Example**  

Refer to the following table for a **complete example**  :

| LN | Class Definition                                             | SQL Clauses                                            |
|----|--------------------------------------------------------------|--------------------------------------------------------|
| 1  | @View(CustomerEntity.class)                                  | SELECT                                                 |
| 2  | @View(OrdersEntity.class)                                    |                                                        |
| 3  | @View(LineitemEntity.class)                                  |                                                        |
| 4  | public class ShippingPriorityView {                          |                                                        |
| 5  | @GroupBy                                                     |                                                        |
| 6  | private String l\_orderkey;                                  | l\_orderkey,                                           |
| 7  | @Column(name = "SUM(l\_extendedprice \* (1 - l\_discount))") | SUM(l\_extendedprice \* (1 - l\_discount)) AS revenue, |
| 8  | private Double revenue;                                      |                                                        |
| 9  | @GroupBy                                                     |                                                        |
| 10 | private Date o\_orderdate;                                   | o\_orderdate,                                          |
| 11 | @GroupBy                                                     |                                                        |
| 12 | private String o\_shippriority;                              | o\_shippriority                                        |
| 13 | }                                                            | FROM customer, orders, lineitem                        |
|    | // @View(CustomerEntity.class)                               | WHERE o\_custkey = c\_custkey                          |
|    | // @View(OrdersEntity.class)                                 | AND l\_orderkey = o\_orderkey                          |
|    | // @View(LineitemEntity.class)                               |                                                        |
| 1  | public class ShippingPriorityQuery extends PageQuery {       |                                                        |
| 2  | private String c\_mktsegment;                                | AND c\_mktsegment = ?                                  |
| 3  | private Date o\_orderdateLt;                                 | AND o\_orderdate < ?                                   |
| 4  | private Date l\_shipdateGt;                                  | AND l\_shipdate > ?                                    |
| 5  | }                                                            | GROUP BY l\_orderkey, o\_orderdate, o\_shippriority    |
|    | // PageQuery.sort = "revenue,DESC;o\_orderdate"              | ORDER BY revenue DESC, o\_orderdate                    |
