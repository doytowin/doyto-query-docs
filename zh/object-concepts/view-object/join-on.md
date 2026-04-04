# JOIN/ON映射

通过JOIN/ON关键字实现的连接统一使用`@Join`进行映射。

这类连接在 `ON` 关键字后添加动态查询条件。为此，引入新的查询对象映射 `ON` 子句的条件，并定义为主查询对象的字段。

为此，设计了新注解 `@Join`，用于映射连接表和指定连接类型，同时复用已有的 `@ForeignKey` 注解映射连接条件。

| LN | 对象代码                                                             | SQL 语句                         |
| -- | ---------------------------------------------------------------- | ------------------------------ |
| 1  | public class CustomerOrdersQuery extends PageQuery {             |                                |
| 2  |   @Join(from = @View(value = CustomerEntity.class, alias = "c"), | FROM customer c                |
| 3  |     type = Join.JoinType.LEFT\_JOIN,                             | LEFT JOIN orders o             |
| 4  |     join = @View(value = OrdersEntity.class, alias = "o"))       | ON o.o\_custkey = c.c\_custkey |
| 5  |   private JoinOrders joinOrders;                                 | AND o.o\_comment NOT LIKE ?    |
| 6  | }                                                                |                                |
