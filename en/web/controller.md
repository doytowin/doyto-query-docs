# Controller

### AbstractEIQController\<E, I, Q>

#### Example

```java
package win.doyto.query.web.demo.module.role;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import win.doyto.query.test.role.RoleEntity;
import win.doyto.query.test.role.RoleQuery;
import win.doyto.query.web.controller.AbstractEIQController;

@RestController
@RequestMapping("role")
public class RoleController extends AbstractEIQController<RoleEntity, Integer, RoleQuery> {
}

```

### AbstractRestController\<E, I, Q, R, S>

### AbstractDynamicController\<E, I, Q, R, S, W>

`AbstractDynamicController` is for table sharding.

#### Example

```java
package win.doyto.query.web.demo.module.menu;

import com.fasterxml.jackson.core.type.TypeReference;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import win.doyto.query.web.controller.AbstractDynamicController;
import win.doyto.query.web.response.JsonBody;

@JsonBody
@RestController
@RequestMapping("{platform}/menu")
class MenuController extends AbstractDynamicController<MenuEntity, Integer, MenuQuery, MenuRequest, MenuResponse, MenuIdWrapper> {

    public MenuController(MenuService menuService) {
        super(menuService, new TypeReference<>() {});
    }

}
```

With a dynamic service `MenuService`:

```java
@Service
public class MenuService extends AbstractDynamicService<MenuEntity, Integer, MenuQuery> {

}
```
