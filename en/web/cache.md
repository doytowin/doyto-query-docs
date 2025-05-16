# Cache

There are two ways to enable caching for target entities.

## Configuration

Specify their class names using the `doyto.query.caches` property in the `application.yaml` file:

```yaml
doyto:
  query:
    caches: UserEntity, MenuEntity
```

## Programmatic

Override method `getCacheName()` in `AbstractDynamicService`:

```java
public class UserService extends AbstractCrudService<UserEntity, Integer, UserQuery> {
    @Override
    protected String getCacheName() {
        return "module:user"; // return any string except UserEntity 
    }
}
```

