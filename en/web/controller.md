# Controller



### AbstractEIQController\<&#x45;_, I, Q>_

### AbstractRestController\<&#x45;_, I, Q, R, S>_

### AbstractDynamicController\<&#x45;_, I, Q, R, S, W>_

#### _Example_

```java
@JsonBody
@RestController
@RequestMapping("{platform}/menu")
class MenuController extends AbstractDynamicController<MenuEntity, Integer, MenuQuery, MenuRequest, MenuResponse, MenuIdWrapper> {

    public MenuController(MenuService menuService) {
        super(menuService, new TypeReference<>() {});
    }

}

@Service
public class MenuService extends AbstractDynamicService<MenuEntity, Integer, MenuQuery> {

}

```
