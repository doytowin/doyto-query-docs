# 逻辑后缀字段

默认情况下，查询对象各个字段对应的查询条件之间通过AND连接。如果想要定义使用逻辑运算符OR连接的查询条件，则需要在字段名称中定义后缀Or，并且支持以下三种字段类型：

```java
public class UserQuery extends PageQuery {
    // ...
    private List<String> nameStartOr;
    private UserQuery userOr;
    private List<UserQuery> usersOr;
}
```

### List\<String\> nameStartOr;

```java
UserQuery userQuery = UserQuery.builder().nameStartOr(List.of("Bob","John","Tim")).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE (name LIKE ? OR name LIKE ? OR name LIKE ?)" args="[Bob% John% Tim%]"
```

### UserQuery userOr;

```java
UserQuery userQuery = UserQuery.builder().nameStartOr(Arrays.asList(1, 4, 12)).deleted(trur).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user 
// WHERE (id IN (?, ?, ?) OR deleted = ?)" args="[1 4 12 true]"
```

### List\<UserQuery> usersOr;

```java
UserQuery userQuery = UserQuery.builder()
    .usersOr(List.of(
        UserQuery.builder().idIn(List.of(1L, 4L, 12L)).deleted(true).build(),
        UserQuery.builder().idGt(10L).deleted(false).build()
    )).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user
// WHERE (id IN (?, ?, ?) AND deleted = ? OR id > ? AND deleted = ?)"
// args="[1 4 12 true 10 false]"
```

## And后缀

当字段的名称以`And`结尾时，连接多个查询条件的逻辑运算符为AND。

```java
public class UserQuery extends PageQuery {
    // ...
    private UserQuery userOr;
    private UserQuery userAnd;
}
```

### UserAnd \*UserQuery

```java
UserQuery userAnd = UserQuery.builder().idIn(List.of(1L, 4L, 12L)).deleted(false).build();
UserQuery userOr = UserQuery.builder().deleted(true).userAnd(userAnd).build();
UserQuery userQuery = UserQuery.builder().scoreLt(80).userOr(userOr).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user
// WHERE score < ? AND (deleted = ? OR id IN (?, ?, ?) AND deleted = ?)"
// args="[80 true 1 4 12 false]"
```

### 相关文章

[在GoooQo中怎么表达select * from user where id = ? or (name = ? and age = ?)](https://blog.doyto.win/post/goooqo-or-clause/)
