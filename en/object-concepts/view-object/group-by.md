# Grouping Clause Mapping

For the `GROUP BY` clause, grouping fields typically also need to appear in the returned columns. This can be achieved by adding an annotation to the field in the view class to declare it as a grouping column, which will then be appended to the `GROUP BY` clause during mapping.

**Example**

When defining a view object, add the `@GroupBy` annotation to the fields used for grouping:

```java
@GroupBy
private int returnFlag;

@GroupBy
private int lineStatus;
```

Fields marked with the `@GroupBy` annotation are mapped as:

```sql
SELECT return_flag, line_status, ... FROM ...
GROUP BY return_flag, line_status ...
```
