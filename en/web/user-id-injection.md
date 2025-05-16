# User ID injection

AbstractEntity

```java
public interface CreateUserAware<I extends Serializable> {

    void setCreateUserId(I createUser);

}
```

\
\


```java
public interface UpdateUserAware<I extends Serializable> {

    void setUpdateUserId(I updateUser);

}
```

