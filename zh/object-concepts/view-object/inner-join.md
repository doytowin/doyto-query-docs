# 内连接

以TPC-H基准测试的第3条查询“Shipping Priority Query”为例，演示如何映射表连接。该查询语句如下：

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

这是典型的多表交叉连接，需处理以下两部分：

1. 多个表名：`FROM customer, orders, lineitem`
2. 连接条件：`WHERE o_custkey = c_custkey AND l_orderkey = o_orderkey`

### 多表名映射

**定义**
通过视图类上的注解 `@ComplexView` 和 `@View` 来配置连接的实体：

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

**配置**
针对运输优先级查询，定义如下视图类：

```java
@View(CustomerEntity.class)
@View(OrdersEntity.class)
@View(LineitemEntity.class)
public class ShippingPriorityView { /*...*/ }
```

**解析**
通过注解配置，自动获取表名 `customer, orders, lineitem`。

### 连接条件映射

**定义**
通过注解 `@ForeignKey` 配置实体外键字段，确定实体间关系：

```java
@Target(FIELD)
@Retention(RUNTIME)
public @interface ForeignKey {
    Class<?> entity();
    String field();
}
```

**示例**
在 `OrdersEntity` 中配置外键字段：

```java
public class OrdersEntity extends AbstractPersistable<Long> {
  private String o_orderkey;
  @ForeignKey(entity = CustomerEntity.class, field = "c_custkey")
  private String o_custkey;
  //...
}
```

**解析**
当视图类用 `@View` 注解配置后，会扫描其他连接实体，查找外键对应实体，若存在则加入连接条件。

在 `ShippingPriorityView` 中找到 `OrdersEntity` 的 `o_custkey` 对应 `CustomerEntity` 的 `c_custkey`，添加连接条件 `o_custkey = c_custkey`，同理添加 `l_orderkey = o_orderkey`。

处理的 SQL 部分为：

```sql
FROM  customer, orders, lineitem
WHERE o_custkey = c_custkey
AND   l_orderkey = o_orderkey
```
