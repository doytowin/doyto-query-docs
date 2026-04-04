# 增删查改接口

## 接口定义

`DataAccess`接口提供访问数据库的增删查改方法。

```java
public interface DataAccess<E extends Persistable<I>, I extends Serializable, Q extends DoytoQuery> {
    List<E> query(Q query);
    long count(Q query);
    PageList<E> page(Q query);
    <V> List<V> queryColumns(Q q, Class<V> clazz, String... columns);
    List<I> queryIds(Q query);

    default E get(I id) {
        return get(IdWrapper.build(id));
    }
    E get(IdWrapper<I> w);

    default int delete(I id) {
        return delete(IdWrapper.build(id));
    }
    int delete(IdWrapper<I> w);
    int delete(Q query);

    void create(E e);
    default int batchInsert(Iterable<E> entities, String... columns) {
        int count = 0;
        for (E entity : entities) {
            create(entity);
            count++;
        }
        return count;
    }

    int update(E e);
    int patch(E e);
    int patch(E e, Q q);
}
```

`DataAccess`接口中的所有方法一共只接收4类参数：

* `id` 实体的主键；
* `IdWrapper` 分表主键对象，用于分表查询；
* `Entity` 实体对象，用于映射表名和列名；
* `Query` 查询对象，用于动态构造查询条件和分页语句，需要继承`PageQuery`

对于`Entity`的定义，请参考：

{% content-ref url="../object-concepts/entity-object/" %}
[entity-object](../object-concepts/entity-object/)
{% endcontent-ref %}

对于`Query`的定义，请参考：

{% content-ref url="../object-concepts/query-object/" %}
[query-object](../object-concepts/query-object/)
{% endcontent-ref %}

## 示例

以下接口调用基于实体对象`UserEntity`和查询对象`UserQuery`进行演示：

```java
@Getter
@Setter
public class UserEntity extends AbstractCommonEntity<Long, Long> {
    @NotNull(groups = CreateGroup.class)
    private String name;
    private Integer score;
    private String memo;
    private Boolean deleted;
}

@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class UserQuery extends PageQuery {
    private Long idGt;
    private List<Long> idIn;
    private Integer scoreLt;
    private Boolean memoNull;
    private String memoLike;
    private Boolean deleted;
    private List<UserQuery> userOr;

    @QueryField(and = "(username = ? OR email = ?)")
    private String account;

    @Subquery(select = "avg(score)", from = UserEntity.class)
    private UserQuery scoreLtAvg;

    @Subquery(select = "score", from = UserEntity.class)
    private UserQuery scoreLtAny;

    @Subquery(select = "score", from = UserEntity.class)
    private UserQuery scoreLtAll;

    @Subquery(select = "avg(score)", from = UserEntity.class)
    private UserQuery scoreGtAvg;
}

@Bean
public JdbcDataAccess<UserEntity, Long, UserQuery>
userDataAccess(@Autowired DatabaseOperations databaseOperations) {
    return new JdbcDataAccess<>(databaseOperations, UserEntity.class);
}
```

### Get

根据id查询数据：

```java
UserEntity userEntity = userDataAccess.get(3L);
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE id = ?" args="[3]"
```

### Query

根据查询条件查询数据：

```java
// 示例 1
UserQuery userQuery = UserQuery.builder().scoreLt(80).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE score < ?" args="[80]"

// 示例 2
UserQuery userQuery = UserQuery.builder().memoLike("Great").pageSize(20).sort("id,desc;score").build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE memo LIKE ? ORDER BY id DESC, score LIMIT 20 OFFSET 0" args="[Great]"

// 示例 3
UserQuery userQuery = UserQuery.builder().idIn(List.of(1L, 4L, 12L)).deleted(true).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE id IN (?, ?, ?) AND deleted = ?" args="[1 4 12 true]"

// 示例 4
UserQuery userQuery = UserQuery.builder()
    .userOr(List.of(
        UserQuery.builder().idGt(10L).memoNull(true).build(),
        UserQuery.builder().scoreLt(80).memoLike("Good").build()
    ))
    .build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE (id > ? AND memo IS NULL OR score < ? AND memo LIKE ?)" args="[10 80 Good]"

// 示例 5
UserQuery userQuery = UserQuery.builder()
    .scoreGtAvg(UserQuery.builder().deleted(true).build())
    .scoreLtAny(UserQuery.builder().build())
    .build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE score > (SELECT avg(score) FROM t_user WHERE deleted = ?) 
// AND score < ANY(SELECT score FROM t_user)" args="[true]"

// 示例 6
UserQuery userQuery = UserQuery.builder().account("John").build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE (username = ? OR email = ?)" args="[John John]"
```

### Count

根据查询条件查询数据的总数：

```java
UserQuery userQuery = UserQuery.builder().scoreLt(60).build();
long count = userDataAccess.count(userQuery);
// SQL="SELECT count(0) FROM t_user WHERE score < ?" args="[60]"
```

### Page

根据查询条件查询数据和总数：

```java
UserQuery userQuery = UserQuery.builder().scoreLt(80).pageSize(20).build();
PageList<UserEntity> page = userDataAccess.page(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE score < ? LIMIT 20 OFFSET 0" args="[80]"
// SQL="SELECT count(0) FROM t_user WHERE score < ?" args="[80]"
```

### Delete

根据id删除数据：

```java
int deletedCount = userDataAccess.delete(3L);
// SQL="DELETE FROM t_user WHERE id = ?" args="[3]"
```

### DeleteByQuery

根据查询条件删除数据：

```java
UserQuery userQuery = UserQuery.builder().scoreLt(80).build();
int deletedCount = userDataAccess.delete(userQuery);
// SQL="DELETE FROM t_user WHERE score < ?" args="[80]"
```

### Create

创建单条数据：

```java
UserEntity user = new UserEntity();
user.setName("John");
user.setScore(90);
user.setDeleted(false);
userDataAccess.create(user);
// SQL="INSERT INTO t_user (name, score, memo, deleted) VALUES (?, ?, ?, ?)" args="[John, 90, null, false]"
```

### CreateMulti

创建多条数据：

```java
UserEntity user1 = new UserEntity();
user1.setName("John");
user1.setScore(90);
user1.setMemo("Great");
user1.setDeleted(false);
UserEntity user2 = new UserEntity();
user2.setName("Alex");
user2.setScore(55);
List<UserEntity> entities = List.of(user1, user2);
int createdCount = userDataAccess.batchInsert(entities);
// SQL="INSERT INTO t_user (name, score, memo, deleted) VALUES (?, ?, ?, ?), (?, ?, ?, ?)" args="[John, 90, Great, false, Alex, 55, null, null]"
```

### Update

根据id更新所有字段：

```java
UserEntity user = new UserEntity();
user.setId(2L);
user.setScore(90);
user.setMemo("Great");
int updatedCount = userDataAccess.update(user);
// SQL="UPDATE t_user SET score = ?, memo = ? WHERE id = ?" args="[90 Great 2]"
```

### Patch

根据id更新所有非空字段：

```java
UserEntity user = new UserEntity();
user.setId(2L);
user.setScore(90);
int patchedCount = userDataAccess.patch(user);
// SQL="UPDATE t_user SET score = ? WHERE id = ?" args="[90 2]"
```

### PatchByQuery

根据查询条件更新所有非空字段：

```java
UserEntity user = new UserEntity();
user.setMemo("Add Memo");
UserQuery query = UserQuery.builder().memoNull(true).build();
int patchedCount = userDataAccess.patch(user, query);
// SQL="UPDATE t_user SET memo = ? WHERE memo IS NULL" args="[Add Memo]"
```
