# ER关系字段

### 抽象实体路径

在实体关系图中，多对多关系用于表示两个实体之间的关系。多对多关系是具有传递性的。例如，如果实体A与实体B具有多对多关系，而实体B与实体C具有多对多关系，则实体A和实体C也具有多对多关系，这是一种间接的多对多关系。

基于多对多关系的传递性，**抽象实体路径**的概念被提出用于描述实体之间这种直接或间接的多对多关系。抽象实体路径将从一个实体到另一个实体的所有实体作为节点来描述任意两个实体之间所具有的多对多关系。例如，实体A和实体B的抽象实体路径为\[A,B]，实体B和实体A的抽象实体路径为\[B,A]，实体C和实体A的抽象实体路径为\[C,B,A]。抽象实体路径包含了任意两个实体之间关系的全部信息，从而用于动态生成复杂的嵌套查询语句。

DoytoQuery引入**抽象实体路径**概念，定义注解`@DomainPath`来定义实体之间的关系。该标签用于查询对象中的用于查询实体关系的字段。例如，实体路径`` `entitypath:"user,role"` ``，基于预定的表名格式，可以得到两个实体表名t\_user和t\_role，中间表表名a\_user\_and\_role，以及两个外键名称user\_id和role\_id，进而生成查询语句：

```sql
SELECT * FROM t_user WHERE
id IN (
    SELECT user_id FROM a_user_and_role WHERE role_id IN (
       SELECT id FROM t_role WHERE ...
    )
)
```

### 示例

表 `t_menu` 有一个列 `parent_id`，它将 `id` 列本身引用为外键。`parent_id` 列用于定义菜单项之间的层次父子关系。菜单通过通用 RBAC 模型作为系统资源分配给用户。那么菜单到用户的实体路径即为：`menu,perm,role,user`，用于生成嵌套查询语句。

## 嵌套查询

DoytoQuery通过解析字段上配置的@DomainPath注解来为字段生成嵌套查询语句。

注解定义如下：

{% code title="DomainPath.java" %}
```java
@Target(FIELD)
@Retention(RUNTIME)
public @interface DomainPath {
    /**
     * To describe how to route from the host domain to the target domain.
     *
     * @return paths array
     */
    String[] value();

    String localAlias() default  "t";

    /**
     * The field in this domain to maintain the relationship with the target domain.
     *
     * @return name of the local field
     */
    String localField() default "id";

    /**
     * The field in another domain to maintain the relationship with this domain.
     *
     * @return name of the foreign field
     */
    String foreignField() default "id";

    String foreignAlias() default "t1";
}

```
{% endcode %}

#### 简单嵌套查询

假设一个具有层级关系的菜单表，子菜单的`parent_id`指向父菜单的`id`，这样可以通过如下语句查询出所有的父菜单：

```sql
SELECT * FROM menu WHERE id IN (SELECT parent_id FROM menu)
```

通过DoytoQuery执行该查询，需要创建对应的MenuQuery类，并添加一个用于查询的字段，配置@NestedQueries注解：

```java

@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class MenuQuery extends PageQuery {
    // many-to-one
    @DomainPath(value = "menu", localField = "parentId")
    private MenuQuery parent;

    // one-to-many
    @DomainPath(value = "menu", foreignField = "parentId")
    private MenuQuery children;

    @DomainPath({"menu", "perm", "role", "user"})
    private UserQuery user;

    private String nameLike;
    private Boolean valid;
}

```
