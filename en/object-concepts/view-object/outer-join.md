# Outer Join

Connections implemented through the `JOIN/ON` keywords uniformly use `@Join` for mapping.

This type of connection adds dynamic query conditions after the `ON` keyword. To achieve this, a new query object is
introduced to map the conditions of the `ON` clause, and it is defined as a field of the main query object.

For this purpose, a new annotation `@Join` is designed to map the joined tables and specify the join type, while reusing
the existing `@ForeignKey` annotation to map the join conditions.

| LN | Class Definition                                               | SQL Clauses                    |
|----|----------------------------------------------------------------|--------------------------------|
| 1  | public class CustomerOrdersQuery extends PageQuery {           |                                |
| 2  | @Join(from = @View(value = CustomerEntity.class, alias = "c"), | FROM customer c                |
| 3  | type = Join.JoinType.LEFT\_JOIN,                               | LEFT JOIN orders o             |
| 4  | join = @View(value = OrdersEntity.class, alias = "o"))         | ON o.o\_custkey = c.c\_custkey |
| 5  | private JoinOrders joinOrders;                                 | AND o.o\_comment NOT LIKE ?    |
| 6  | }                                                              |                                |
