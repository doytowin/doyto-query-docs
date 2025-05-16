# Pessimistic lock

## Shared lock&#x20;

To acquire a **shared lock** on the queried rows, assign `PESSIMISTIC_READ` to the `lockMode` field of the `PageQuery` class. This will generate an SQL statement with a `FOR SHARE` clause, allowing other transactions to read the same rows but preventing them from updating or deleting them until the current transaction is complete.

```java
UserQuery testQuery = UserQuery.builder().id(1).lockMode(LockMode.PESSIMISTIC_READ).build();
List<UserEntity> lockedUsers = userService.query(userQuery);
// SQL: SELECT * FROM t_user t WHERE id = ? FOR SHARE
```

## Exclusive lock

To acquire an **exclusive lock** on queried rows, assign `PESSIMISTIC_WRITE` to the `lockMode` field of the `PageQuery` class. This appends a `FOR UPDATE` clause to the SQL query, locking the selected rows for the duration of the transaction and preventing concurrent access by other transactions.

```java
UserQuery testQuery = UserQuery.builder().id(1).lockMode(LockMode.PESSIMISTIC_WRITE).build();
List<UserEntity> lockedUsers = userService.query(userQuery);
// SQL: SELECT * FROM t_user t WHERE id = ? FOR UPDATE
```
