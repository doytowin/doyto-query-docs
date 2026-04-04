# PageQuery

A query object needs to inherit the `PageQuery` class to construct pagination and sorting clauses. The `PageQuery` class defines three fields, where `pageNumber` and `pageSize` are used to build the pagination clause, and `sort` is used to build the sorting clause.  

## Example  

### Definition  

```java
@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class UserQuery extends PageQuery {
    private Long idGt;
    //...
}
```

### Pagination  

```java
UserQuery userQuery = UserQuery.builder().build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User

UserQuery userQuery = UserQuery.builder().pageSize(20).build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User LIMIT 20 OFFSET 0

// When only PageNumber is set, PageSize will be set to 10
UserQuery userQuery = UserQuery.builder().pageNumber(5).build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User LIMIT 10 OFFSET 40

UserQuery userQuery = UserQuery.builder().pageNumber(3).pageSize(50).build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User LIMIT 10 OFFSET 100	
```

### Sorting  

```java
UserQuery userQuery = UserQuery.builder().sort("id,desc;score,asc;memo").build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User ORDER BY id DESC, score ASC, memo
```

{% hint style="info" %}
The string assigned to the `sort` field must conform to the regular expression: `PageQuery.SORT_PTN`.  
{% endhint %}
