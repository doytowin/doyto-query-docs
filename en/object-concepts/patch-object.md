# Patch Object

DoytoQuery supports **incremental updates** on numeric fields using special suffixes in the patch object.;

## Suffix Mapping

For example, to increase a user's score by a certain amount without first reading the original value, you can use a field like `scoreAe` (short for _Add/Extend_).

```java
public class UserPatch extends UserEntity {
    private Integer scoreAe;
}
```

```java
UserEntity userPatch = UserPatch.builder().id(1).valid(true).scoreAe(20).build();
userDataAccess.patch(userPatch);
// SQL: UPDATE t_user SET valid = ?, score = score + ? WHERE id = ?
```

## Annotation Mapping

Use `@Clause` to define a custom condition:

```java
public class UserPatch extends UserEntity {
    @Clause("score = score + ?")
    private Integer scoreAe;
}
```

