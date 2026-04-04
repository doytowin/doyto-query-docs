# Entity Object

Entity objects are used to provide table names and column names for constructing CRUD statements. They need to implement the `Persistable` interface or inherit base classes such as `AbstractPersistable`, `AbstractEntity`, or `AbstractCommonEntity`.

## Example

```java
@Getter
@Setter
public class UserEntity extends AbstractCommonEntity<Long, Long> {
    private String name;
    private Integer score;
    private String memo;
}
```

In the example, the CRUD statements corresponding to `UserEntity` are:

```sql
SELECT id, name, score, memo FROM t_user；
INSERT INTO t_user (name, score, memo) VALUES (?, ?, ?)
UPDATE t_user SET name = ?, score = ?, memo = ? WHERE id = ?;
DELETE FROM t_user WHERE id = ?;
```

