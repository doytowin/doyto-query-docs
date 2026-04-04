# Logical Suffix Fields

By default, the query conditions corresponding to each field in a query object are connected with the **AND** operator.  
If you want to define query conditions connected with the **OR** operator, you need to define the suffix **`Or`** in the field name, and the following three field types are supported:

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

## And Suffix

When a field name ends with **`And`**, the logical operator connecting multiple query conditions is **AND**.

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

### Related Article

[How to express `select * from user where id = ? or name = ? and age = ?` in GoooQo](https://blog.doyto.win/post/goooqo-or-clause-en/)
