# Subquery Field

For typical subquery conditions, e.g., `score > (SELECT avg(score) FROM t_user WHERE removed = ?)`, is divided into three parts for separate mapping:

* score >
* SELECT avg(score) FROM t_user
* WHERE clause

The first part, score >, can be mapped using a field name like scoreGtXxx.
The string defined after the predicate suffix is only used to distinguish duplicate field names and is ignored during mapping.

The second part contains a column name and a table name, which are static and unchanging. DoytoQuery provides two annotations to store this information:

- `@Subquery` defines the column and table for the subquery statement;
- `@SubqueryV2` defines a view object, which is used in combination with field values to generate the subquery statement.

The third part is another WHERE clause, which can be mapped through a Query Object.
Therefore, we define the field type as the corresponding Query Object and use the Query Object’s mapping method to map field values into the subquery’s WHERE clause.

## Example

**Annotation `@Subquery`：**

```java
@SuperBuilder
@NoArgsConstructor
public class UserQuery extends PageQuery {
    // ...
    
    @Subquery(select = "avg(score)", from = UserEntity.class)
    private UserQuery scoreLtAvg;

    @Subquery(select = "score", from = UserEntity.class)
    private UserQuery scoreLtAny;

    @Subquery(select = "score", from = UserEntity.class)
    private UserQuery scoreLtAll;

    @Subquery(select = "avg(score)", from = UserEntity.class)
    private UserQuery scoreGtAvg;
}
```

**Annotation `@SubqueryV2`：**

```java
@Getter
@Setter
@SuperBuilder
@NoArgsConstructor
@AllArgsConstructor
public class MinimumCostSupplierQuery extends PageQuery {
    private Integer p_size;
    private String p_typeEnd;
    private String r_name;
    
    // Subquery comdition: ps_supplycost = SELECT min(ps_supplycost) FROM partsupp, supplier, nation, region WHERE ...
    @SubqueryV2(MinSupplyCostView.class)
    private SupplyCostQuery psSupplycost;

    @View(value = PartEntity.class, context = true)
    @View(PartsuppEntity.class)
    @View(SupplierEntity.class)
    @View(NationEntity.class)
    @View(RegionEntity.class)
    private static class MinSupplyCostView {
        @NoLabel
        private Integer minPs_supplycost;
    }
}
```
