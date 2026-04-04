# 聚合查询对象

SQL中`HAVING`子句用于过滤聚合后的分组记录。`HAVING`子句是可选的，语法类似`WHERE`子句。

`HAVING`子句的映射通过**Having 对象**实现。

映射过程复用查询对象映射算法，字段映射时同时支持前缀映射和后缀映射，例如，Having 对象中定义字段 `avgScoreGe` 会映射为：

```sql
HAVING avg(score) >= ?
```

在 Java 中，Having 对象实现一个空接口 `Having` 以标记其用于构建 HAVING 子句。同时，可以让 Having 对象继承查询对象，查询对象继承 `PageQuery`，形成三级结构。

* `PageQuery` 中的字段用于构建排序和分页子句；
* 查询对象中的字段用于构建 `WHERE` 子句；
* Having 对象中的字段用于构建 `HAVING` 子句。

