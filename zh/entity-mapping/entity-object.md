# 实体对象

## 示例

```java
@Getter
@Setter
public class UserEntity extends AbstractCommonEntity<Long, Long> {
    private String name;
    private Integer score;
    private String memo;
}
```

实体对象用于为CRUD语句的构建提供表名和列名。需要实现`Persistable`接口，或者继承基类`AbstractPersistable`, `AbstractEntity`, `AbstractCommonEntity`.

示例中`UserEntity`对应的增删查改语句为：

```sql
SELECT id, name, score, memo FROM t_user；
INSERT INTO t_user (name, score, memo) VALUES (?, ?, ?)
UPDATE t_user SET name = ?, score = ?, memo = ? WHERE id = ?;
DELETE FROM t_user WHERE id = ?;
```

