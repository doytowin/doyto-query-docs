# Page Query

## 定义

`PageQuery`实现了`Query`接口，定义了三个字段，
其中，`PageNumber`和`PageSize`用于构建分页子句，`Sort` 用于构建排序子句。

```java
@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class PageQuery implements DoytoQuery {

    @SuppressWarnings("java:S5843")
    protected static final String SORT_RX = "(\\w+,(asc|desc)|field\\(\\w+(,[\\w']+)++\\))(;(\\w+,(asc|desc)|field\\(\\w+(,[\\w']+)++\\)))*";
    protected static final Pattern SORT_PTN = Pattern.compile(PageQuery.SORT_RX);

    @Transient
    private Integer pageNumber;

    @Transient
    private Integer pageSize;

    /**
     * Sorting field, format: field1,desc;field2,asc;field(col,'v1','v2')
     */
    @Transient
    @jakarta.validation.constraints.Pattern(regexp = SORT_RX, groups = PageGroup.class)
    private String sort;

    @Transient
    private LockMode lockMode;

    // ...
}

```

## 分页示例

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

## 排序示例

`Sort`赋值的字符串需要符合正则表达式：`PageQuery.SORT_PTN`

```java
UserQuery userQuery = UserQuery.builder().sort("id,desc;score,asc;memo").build();
List<UserEntity> users = userDataAccess.query(userQuery);
//SELECT id, score, memo FROM User ORDER BY id DESC, score ASC, memo
```
