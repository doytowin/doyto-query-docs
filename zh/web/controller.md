# Controller


### AbstractEIQController

请求和响应直接使用实体类作为参数：

```java
@RestController
@RequestMapping("role")
public class RoleController extends AbstractEIQController<RoleEntity, Integer, RoleQuery> {
}
```

### AbstractRestController

请求和响应使用DTO作为参数：

```java
@RestController
@RequestMapping("user")
public class UserController extends AbstractRestController<UserEntity, Long, UserQuery, UserRequest, UserResponse> {
}
```

### AbstractDynamicController

分表Controller：

```java
@JsonBody
@RestController
@RequestMapping("{platform}/menu")
public class MenuController extends AbstractDynamicController<MenuEntity, Integer, MenuQuery, MenuRequest, MenuResponse, MenuIdWrapper> {
    public MenuController(MenuService menuService) {
        super(menuService, new TypeReference<>() {});
    }
}

@Service
public class MenuService extends AbstractDynamicService<MenuEntity, Integer, MenuQuery> {
}

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class MenuIdWrapper implements IdWrapper<Integer> {
    private Integer id;
    private String platform;

    @Override
    public String toCacheKey() {
        return id + "-" + platform;
    }
}
```
