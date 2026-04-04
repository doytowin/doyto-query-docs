# 列映射

视图对象的字段不仅用于映射不同表的列，还用于表示聚合表达式和分组子句。

## 聚合列映射

聚合函数的应用是聚合查询的核心功能。所有通过聚合列查询的数据都需由视图类中定义的字段保存，因此我们需要将字段名映射为聚合列，使用前缀映射是一种很好的选择。

| 前缀         | 聚合函数名        | 字段名             | 聚合列表达式                                 |
| ---------- | ------------ | --------------- | -------------------------------------- |
| sum        | sum          | sumScore        | sum(score) AS sumScore                 |
| max        | max          | maxScore        | max(score) AS maxScore                 |
| min        | min          | minScore        | min(score) AS minScore                 |
| avg        | avg          | avgScore        | avg(score) AS avgScore                 |
| first      | first        | firstScore      | first(score) AS firstScore             |
| last       | last         | lastScore       | last(score) AS lastScore               |
| stdDevPop  | stddev\_pop  | stdDevPopScore  | stddev\_pop(score) AS stdDevPopScore   |
| stdDevSamp | stddev\_samp | stdDevSampScore | stddev\_samp(score) AS stdDevSampScore |
| stdDev     | stddev       | stdDev          | stddev(score) AS stdDev                |
| addToSet   | addToSet     | addToSetScore   | addToSet(score) AS addToSetScore       |
| push       | push         | pushScore       | push(score) AS pushScore               |
| count      | count        | countScore      | count(score) AS countScore             |
| count      | count        | count           | count(\*) AS count                     |

**示例**
如果要计算名为 `score` 的列的平均值，使用 `avg` 函数，定义字段名为 `avgScore`，遵循聚合前缀+列名的命名规则，最终映射为 `avg(score) AS avgScore`。

## 聚合表达式映射

对于无法直接用字段名表示的聚合表达式，可以通过注解指定具体的表达式，将表达式映射为列名，字段名作为标签。

**示例**
定义字段时添加表达式注解：

```java
@Column(name = "sum(l_extendedprice*(1-l_discount))")
private BigDecimal sum_disc_price;
```

该字段映射为：

```sql
sum(l_extendedprice*(1-l_discount)) AS sum_disc_price
```



**完整示例** 请参见表格：

| LN | 对象代码                                                           | SQL 语句                                                 |
| -- | -------------------------------------------------------------- | ------------------------------------------------------ |
| 1  | @View(CustomerEntity.class)                                    | SELECT                                                 |
| 2  | @View(OrdersEntity.class)                                      |                                                        |
| 3  | @View(LineitemEntity.class)                                    |                                                        |
| 4  | public class ShippingPriorityView {                            |                                                        |
| 5  |   @GroupBy                                                     |                                                        |
| 6  |   private String l\_orderkey;                                  | l\_orderkey,                                           |
| 7  |   @Column(name = "SUM(l\_extendedprice \* (1 - l\_discount))") | SUM(l\_extendedprice \* (1 - l\_discount)) AS revenue, |
| 8  |   private Double revenue;                                      |                                                        |
| 9  |   @GroupBy                                                     |                                                        |
| 10 |   private Date o\_orderdate;                                   | o\_orderdate,                                          |
| 11 |   @GroupBy                                                     |                                                        |
| 12 |   private String o\_shippriority;                              | o\_shippriority                                        |
| 13 | }                                                              | FROM customer, orders, lineitem                        |
|    | // @View(CustomerEntity.class)                                 | WHERE o\_custkey = c\_custkey                          |
|    | // @View(OrdersEntity.class)                                   | AND l\_orderkey = o\_orderkey                          |
|    | // @View(LineitemEntity.class)                                 |                                                        |
| 1  | public class ShippingPriorityQuery extends PageQuery {         |                                                        |
| 2  |   private String c\_mktsegment;                                | AND c\_mktsegment = ?                                  |
| 3  |   private Date o\_orderdateLt;                                 | AND o\_orderdate < ?                                   |
| 4  |   private Date l\_shipdateGt;                                  | AND l\_shipdate > ?                                    |
| 5  | }                                                              | GROUP BY l\_orderkey, o\_orderdate, o\_shippriority    |
|    | // PageQuery.sort = "revenue,DESC;o\_orderdate"                | ORDER BY revenue DESC, o\_orderdate                    |
