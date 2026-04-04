# Inner Join

Taking the third query "Shipping Priority Query" from the TPC-H benchmark as an example, this section demonstrates how to map table joins. The query statement is as follows:

```sql
SELECT l_orderkey, SUM(l_extendedprice * (1 - l_discount)) AS revenue, o_orderdate, o_shippriority
FROM customer, orders, lineitem
WHERE o_custkey = c_custkey
  AND l_orderkey = o_orderkey
  AND c_mktsegment = ?
  AND o_orderdate < ?
  AND l_shipdate > ?
GROUP BY l_orderkey, o_orderdate, o_shippriority
ORDER BY revenue DESC, o_orderdate
```

This is a typical multi-table cross-join, requiring handling of the following two parts:

1. **Multiple table names**: `FROM customer, orders, lineitem`
2. **Join conditions**: `WHERE o_custkey = c_custkey AND l_orderkey = o_orderkey`

### Multiple Table Name Mapping

**Definition**  
Configure the joined entities through the annotations `@ComplexView` and `@View` on the view class:

```java
@Target(TYPE)
@Retention(RUNTIME)
public @interface ComplexView {
    View[] value();
}

@Target(TYPE)
@Retention(RUNTIME)
@Repeatable(ComplexView.class)
public @interface View {
    Class<?> value();
    String alias() default "";
    ViewType type() default ViewType.TABLE_NAME;
}
```

**Configuration**  
For the shipping priority query, define the following view class:

```java
@View(CustomerEntity.class)
@View(OrdersEntity.class)
@View(LineitemEntity.class)
public class ShippingPriorityView { /*...*/ }
```

**Parsing**  
Through annotation configuration, the table names `customer, orders, lineitem` are automatically obtained.

### Join Condition Mapping

**Definition**  
Configure the foreign key fields of an entity through the annotation `@ForeignKey` to determine the relationships between entities:

```java
@Target(FIELD)
@Retention(RUNTIME)
public @interface ForeignKey {
    Class<?> entity();
    String field();
}
```

**Example**  
Configure the foreign key field in `OrdersEntity`:

```java
public class OrdersEntity extends AbstractPersistable<Long> {
  private String o_orderkey;
  @ForeignKey(entity = CustomerEntity.class, field = "c_custkey")
  private String o_custkey;
  //...
}
```

**Parsing**  
When a view class is configured with the `@View` annotation, other joined entities are scanned. If a foreign key corresponding to another entity is found, the join condition is added.

In `ShippingPriorityView`, it is found that `o_custkey` in `OrdersEntity` corresponds to `c_custkey` in `CustomerEntity`, so the join condition `o_custkey = c_custkey` is added. Similarly, `l_orderkey = o_orderkey` is added.

The processed SQL portion is:

```sql
FROM  customer, orders, lineitem
WHERE o_custkey = c_custkey
AND   l_orderkey = o_orderkey
```
