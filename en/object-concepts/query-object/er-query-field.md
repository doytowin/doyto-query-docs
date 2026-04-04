# ER Relationship Fields

### Abstract Entity Path

In an entity-relationship diagram, a many-to-many relationship is used to represent the association between two entities. Many-to-many relationships are transitive. For example, if entity A has a many-to-many relationship with entity B, and entity B has a many-to-many relationship with entity C, then entities A and C also have a many-to-many relationship, which is an indirect many-to-many relationship.

Based on the transitivity of many-to-many relationships, the concept of an **Abstract Entity Path** is proposed to describe this direct or indirect many-to-many relationship between entities. An abstract entity path describes the many-to-many relationship between any two entities by considering all entities from one entity to another as nodes. For example, the abstract entity path between entity A and entity B is [A, B]; the path between entity B and entity A is [B, A]; and the path between entity C and entity A is [C, B, A]. The abstract entity path contains all the information about the relationship between any two entities, thereby enabling the dynamic generation of complex nested query statements.

DoytoQuery introduces the concept of an **Abstract Entity Path** and defines the annotation `@DomainPath` to describe relationships between entities. This tag is used for fields in query objects that are intended to query entity relationships. For example, the entity path `` `entitypath:"user,role"` `` can, based on a predefined table name format, yield two entity table names `t_user` and `t_role`, an intermediate table name `a_user_and_role`, and two foreign key names `user_id` and `role_id`. This information is then used to generate a query statement:

```sql
SELECT * FROM t_user WHERE
id IN (
    SELECT user_id FROM a_user_and_role WHERE role_id IN (
       SELECT id FROM t_role WHERE ...
    )
)
```

### Example

The table `t_menu` has a column `parent_id` that references the `id` column itself as a foreign key. The `parent_id` column is used to define hierarchical parent-child relationships between menu items. Menus are assigned to users as system resources through a general RBAC model. Therefore, the entity path from a menu to a user is: `menu, perm, role, user`, which is used to generate nested query statements.

## Nested Queries

DoytoQuery generates nested query statements for fields by parsing the `@DomainPath` annotation configured on the fields.

The annotation is defined as follows:

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

#### Simple Nested Queries

Assume a hierarchical menu table where a child menu's `parent_id` points to the parent menu's `id`. Using this, all parent menus can be queried with the following statement:

```sql
SELECT * FROM menu WHERE id IN (SELECT parent_id FROM menu)
```

To execute this query via DoytoQuery, you need to create the corresponding `MenuQuery` class and add a field for the query, configured with the `@NestedQueries` annotation:

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
