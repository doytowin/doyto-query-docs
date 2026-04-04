# Quick Start

1. Initialize the project on Spring Initializer with the following 4 dependencies:
* Lombok
* Spring Web
* Validation
* \[A database driver]

2. Add DoytoQuery dependencies in pom.xml:
```xml
<dependency>
    <groupId>win.doyto</groupId>
    <artifactId>doyto-query-jdbc</artifactId>
    <version>2.1.0</version>
</dependency>
<dependency>
    <groupId>win.doyto</groupId>
    <artifactId>doyto-query-web</artifactId>
    <version>2.1.0</version>
</dependency>
<dependency>
    <groupId>win.doyto</groupId>
    <artifactId>doyto-query-dialect</artifactId>
    <version>2.1.0</version>
</dependency>
```

3. Define entity and query objects for a table:
```java
@Getter
@Setter
@Entity(name = "user")
public class UserEntity extends AbstractPersistable<Long> {
    private String username;
    private Integer age;
    private Boolean valid;
}

@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class UserQuery extends PageQuery {
    private String username;
    private Integer ageGe;
    private Integer ageLt;
    private Boolean valid;
}
```

Invoking the method [`DataAccess#query(Q)`](https://github.com/doytowin/doyto-query/blob/main/doyto-query-api/src/main/java/win/doyto/query/core/DataAccess.java) in `UserService`:
```java
@Service
public class UserService extends AbstractCrudService<UserEntity, Long, UserQuery> {
    public List<UserEntity> findValidAdultUsers() {
        UserQuery userQuery = UserQuery.builder().ageGe(20).valid(true).pageSize(10).build();
        // Executed SQL: SELECT username, email, valid, id FROM t_user WHERE age >= ? AND valid = ? LIMIT 10 OFFSET 0
        // Parameters  : 20(java.lang.Integer), true(java.lang.Boolean)
        return dataAccess.query(userQuery);
    }
}
```

Define a controller to support RESTful API:
```java
@RestController
@RequestMapping("user")
public class UserController extends AbstractEIQController<UserEntity, Long, UserQuery> {
}
```

Set the log level of `logging.level.org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping` to `trace`,
and after starting the Spring Boot application, you can see in the console that the RESTful interface is ready for `/user/`:

```
s.w.s.m.m.a.RequestMappingHandlerMapping : 
	w.d.q.d.m.u.UserController:
	{PUT [/user/{id}]}: update(Persistable)
	{DELETE [/user/{id}]}: remove(Serializable)
	{GET [/user/{id}]}: get(Serializable)
	{DELETE [/user/]}: delete(DoytoQuery)
	{PATCH [/user/]}: patch(Object,DoytoQuery)
	{GET [/user/]}: page(DoytoQuery)
	{POST [/user/]}: create(List)
	{PATCH [/user/{id}]}: patch(Object)
```

Refer to the [demo](https://github.com/doytowin/doyto-query-demo) for more details.
