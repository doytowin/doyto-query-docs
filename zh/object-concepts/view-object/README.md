# 视图对象

复杂查询通常涉及多个表的连接和列的聚合操作。虽然查询对象可以复用来构造`WHERE`子句，但实体对象无法完整表达查询的其他部分。因此，我们引入专门的**视图对象**来定义复杂查询中的静态部分，同时引入单独的**Having对象**用于生成`HAVING`子句。此外，视图对象也作为结果映射的目标。

为了实现复杂查询的自动映射，我们将映射过程划分为以下三部分：

1. **列映射**：视图对象中定义的字段用于映射查询结果中的所需列，包括普通列和聚合列。
2. **连接映射**：视图对象中的注解用于定义表之间的关系，支持自动生成必要的连接语句。
3. **HAVING子句映射**：Having对象用于生成HAVING子句，支持基于聚合结果的过滤条件。

该设计不仅提高了开发者构造和维护复杂查询语句的效率，也减少了手动拼装SQL带来的风险，显著提升了代码的可读性、可重用性和可维护性。



## 聚合查询接口

除了为单表数据访问设计接口外，还需为聚合查询设计单独接口。

设计并实现了接口 `AggregateClient`，定义如下：
该接口提供通用方法 `query`，允许开发者通过指定目标视图类和查询对象执行聚合查询，结果映射为对应视图对象列表。


```java
public interface AggregateClient {
    <V> AggregateChain<V> aggregate(Class<V> viewClass);

    default <V> List<V> query(Class<V> viewClass, DoytoQuery query) {
        return aggregate(viewClass).filter(query).query();
    }

    default <V> long count(Class<V> viewClass, DoytoQuery query) {
        return aggregate(viewClass).filter(query).count();
    }

    default <V> PageList<V> page(Class<V> viewClass, DoytoQuery query) {
        return aggregate(viewClass).filter(query).page();
    }
}
```

