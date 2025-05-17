# 自定义查询字段

## 使用场景

当遇到现有的字段映射方法无法支持的SQL语句时，可以使用注解`@QueryField`自定义SQL语句，作为临时方案。

## 注解定义

{% code title="QueryField.java" %}
```java
@Target(FIELD)
@Retention(RUNTIME)
public @interface QueryField {
    String and();
}
```
{% endcode %}

{% hint style="info" %}
当被注解字段的值满足过滤条件时，and变量里定义的条件语句将会被拼接到SQL中。
{% endhint %}

## 代码示例

**业务代码：**

```java
public class TestQuery extends PageQuery {
    @QueryField(and = "(username = ? OR email = ? OR mobile = ?)")
    private String account;
}
```

**单元测试：**

```java
@Test
void testQueryField() {
    TestQuery testQuery = TestQuery.builder().account("test").build();
    ArrayList<Object> argList = new ArrayList<>();

    String sql = BuildHelper.buildWhere(testQuery, argList);

    assertThat(sql).isEqualTo(" WHERE (username = ? OR email = ? OR mobile = ?)");
    assertThat(argList).containsExactly("test", "test", "test");
}
```

`TestQuery`的account字段通过`@QueryField`注解定义的查询条件被原样拼接到WHERE语句，并且因为查询条件里有3个占位符，account的值"test"也被三次添加到argList。
