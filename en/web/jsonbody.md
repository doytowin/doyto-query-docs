# @JsonBody

`@JsonBody` is used to automatically wrap data returned to the client.

## Annotation Usage

When `@JsonBody` is applied at the class level of a controller, the return values of all mapping methods in that class will be wrapped inside the `data` field of a `JsonResponse`.

When `@JsonBody` is applied at the method level on a Controller's mapping method, only the return value of that specific method will be wrapped.

### Example

```java
@RestController
@RequestMapping("user")
@JsonBody
public class UserController extends AbstractRestController<UserEntity, Long, UserQuery, UserRequest, UserResponse> {
    //...
}
```

The format of return value:

```json
{
  "code": "0",
  "message": "ok",
  "data": {}
}
```
