# Aggregate Query Object

The `HAVING` clause in SQL is used to filter grouped records after aggregation. The `HAVING` clause is optional, and its syntax is similar to that of the `WHERE` clause.

The mapping of the `HAVING` clause is implemented through a **Having object**.

The mapping process reuses the query object mapping algorithm, supporting both prefix mapping and suffix mapping for field mapping. For example, defining a field `avgScoreGe` in a Having object will be mapped to:

```sql
HAVING avg(score) >= ?
```

In Java, a Having object implements an empty interface `Having` to mark that it is used for constructing the HAVING clause. Meanwhile, the Having object can extend a query object, and the query object can extend `PageQuery`, forming a three-level structure.

* Fields in `PageQuery` are used to construct the `ORDER BY` and pagination clauses;
* Fields in the query object are used to construct the `WHERE` clause;
* Fields in the Having object are used to construct the `HAVING` clause.