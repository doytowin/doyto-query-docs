# 关联实体查询

DoytoQuery支持使用`@DomainPath`注解定义在实体对象的字段上从而同时查询实体及其关联实体。

```java
public class MenuEntity extends AbstractPersistable<Integer> {
    // ...

    @DomainPath({"menu", "perm", "role", "user"})
    private List<UserEntity> users;
}

public class MenuQuery extends PageQuery {
    // ...
    
    private UserQuery withUsers;
}

@Test
void queryMenusWithGrantedUsers() {
    UserQuery usersQuery = UserQuery.builder().build();
    MenuQuery menuQuery = MenuQuery.builder().withUsers(usersQuery).build();
    List<MenuEntity> menuEntities = menuDataAccess.query(menuQuery);
    //...
}
```
