# Custom Query Fields

## Usage Scenario

When encountering SQL statements that cannot be supported by existing field mapping methods, the annotation `@QueryField` can be used to define custom SQL statements as a temporary solution.

## Annotation Definition

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
When the value of the annotated field satisfies the filter condition, the conditional statement defined in the `and` variable will be appended to the SQL statement.
{% endhint %}

## Code Example

**Business Code:**

```java
public class TestQuery extends PageQuery {
    @QueryField(and = "(username = ? OR email = ? OR mobile = ?)")
    private String account;
}
```

**Unit Test:**

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

The query condition defined by the `@QueryField` annotation on the `account` field of `TestQuery` is appended verbatim to the WHERE clause. Because the query condition contains three placeholders, the value "test" of `account` is also added to the `argList` three times.