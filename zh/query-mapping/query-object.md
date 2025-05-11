# 查询对象构建

### 查询对象映射

DoytoQuery通过查询对象动态构建和组合查询条件。
查询对象中的字段用于构建查询条件，根据所构建的查询条件将字段分为多种类型。

```java
@SuperBuilder
@NoArgsConstructor
public class UserQuery extends PageQuery {
    private Long idGt;
    private List<Long> idIn;
    private Integer scoreLt;
    private Boolean memoNull;
    
    //...
}
```

查询对象需要继承分页对象`PageQuery`，以便构建分页子句和排序子句：
{% content-ref url="../page-query.md" %}
[page-query.md](../page-query.md)
{% endcontent-ref %}

### 字段定义

查询对象用于映射 SQL 语句的动态部分，例如过滤条件、分页和排序。

查询对象中的每个字段用于映射一组查询条件。

查看以下文档以了解如何定义查询对象中的字段：

{% content-ref url="predicate-suffix-field.md" %}
[predicate-suffix-field.md](predicate-suffix-field.md)
{% endcontent-ref %}

{% content-ref url="logic-suffix-field.md" %}
[logic-suffix-field.md](logic-suffix-field.md)
{% endcontent-ref %}

{% content-ref url="subquery-field.md" %}
[subquery-field.md](subquery-field.md)
{% endcontent-ref %}

{% content-ref url="er-query-field.md" %}
[er-query-field.md](er-query-field.md)
{% endcontent-ref %}

{% content-ref url="custom-condition-field.md" %}
[custom-condition-field.md](custom-condition-field.md)
{% endcontent-ref %}

