# Intermediate Table Operations

### Table Structure

```sql
create table t_user_and_role (user_id bigint, role_id int);
```

### Bead Declaration

```java
@Bean
public AssociativeService<Long, Integer> userAndRoleAssociativeService() {
    return new TemplateAssociativeService<>("t_user_and_role", "userId", "roleId");
}
```

### Usage

Create a web api to invoke the `userAndRoleAssociativeService`:

```java
@RestController
class AuthController {
    @Resource
    AssociativeService<Long, Integer> userAndRoleAssociativeService;

    @GetPostMapping("reallocateRolesForUser")
    public void reallocateRoles(Long userId, @RequestParam List<Integer> roleIds) {
        userAndRoleAssociativeService.reallocateForLeft(userId, roleIds);
    }
}
```

### Access

Invoking this api will execute the following SQL statement

```sql
DELETE FROM t_user_and_role WHERE userId = ?;
INSERT INTO t_user_and_role (userId, roleId) values (?, ?)[, (?, ?)];
```
