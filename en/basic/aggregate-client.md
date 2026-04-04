# AggregateClient

`AggregateClient` is used to execute complex queries.

## Example

```java
@AllArgsConstructor
@JsonBody
@RestController
public class UserAggregateController {

    private AggregateClient aggregateClient;

    @GetMapping("user/queryCountOfEachLevel")
    public List<UserLevelCountView> queryCountOfEachLevel(UserLevelHaving query) {
        return aggregateClient.query(UserLevelCountView.class, query);
    }

}
```

Refer [View Object](../object-concepts/view-object/README.md) to learn how to define a view object.