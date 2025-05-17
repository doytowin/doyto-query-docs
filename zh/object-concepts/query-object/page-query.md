# 分页对象

查询对象需要继承`PageQuery`类以构造分页子句和排序子句。`PageQuery`类定义了三个字段，\
其中，`PageNumber`和`PageSize`用于构建分页子句，`Sort` 用于构建排序子句。

## 示例

### 定义

```java
@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class UserQuery extends PageQuery {
    private Long idGt;
    //...
}
```

### 分页

```java
UserQuery userQuery = UserQuery.builder().build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User

UserQuery userQuery = UserQuery.builder().pageSize(20).build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User LIMIT 20 OFFSET 0

// When only PageNumber is set, PageSize will be set to 10
UserQuery userQuery = UserQuery.builder().pageNumber(5).build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User LIMIT 10 OFFSET 40

UserQuery userQuery = UserQuery.builder().pageNumber(3).pageSize(50).build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User LIMIT 10 OFFSET 100	
```

### 排序

```java
UserQuery userQuery = UserQuery.builder().sort("id,desc;score,asc;memo").build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User ORDER BY id DESC, score ASC, memo
```

{% hint style="info" %}
赋值给`Sort`字段的字符串需要符合正则表达式：`PageQuery.SORT_PTN。`
{% endhint %}
