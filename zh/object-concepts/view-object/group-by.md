# 分组子句映射

对于`GROUP BY`子句，通常分组字段也需要出现在返回列中，可通过在视图类字段上添加注解声明其为分组列，并在映射时添加到`GROUP BY`后。

**示例**

定义视图对象时，在用于分组的字段上添加`@GroupBy`注解：

```java
@GroupBy
private int returnFlag;

@GroupBy
private int lineStatus;
```

带有`@GroupBy`注解的字段被映射为：

```sql
SELECT return_flag, line_status, ... FROM ...
GROUP BY return_flag, line_status ...
```
