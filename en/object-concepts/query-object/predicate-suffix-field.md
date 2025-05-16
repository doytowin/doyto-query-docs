# Predicate-Suffix Field

## Condition

GoooQo uses a predicate suffix mapping method to map fields in query objects to basic query conditions. Each basic query condition consists of a column name, a comparison operator, and a value. The format of the field name in the query object is a column name plus one of the comparison operator alias. All assigned fields in a query instance will be mapped to the corresponding query conditions and spliced ​​into a query clause.

Here are two examples of the predicate-suffix field mapping:

```go
userQuery := UserQuery{Deleted: P(true)}
users, err := userDataAccess.Query(ctx, userQuery)
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE deleted = ?" args="[true]"

userQuery := UserQuery{IdIn: &[]int64{1, 4, 12}, Deleted: P(true)}
users, err := userDataAccess.Query(ctx, userQuery)
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE id IN (?, ?, ?) AND deleted = ?" args="[1 4 12 true]"
```

Check the [predicate suffix table](predicate-suffix-field.md#predicate-suffix-table) for all supported suffixes by GoooQo.

### Predicate Suffix Table

<table><thead><tr><th>Suffix</th><th>Field Name</th><th>Value</th><th>SQL Condition</th><th data-hidden>MongoDB Condition</th></tr></thead><tbody><tr><td>(EMPTY)</td><td>id</td><td>5</td><td>id = 5</td><td>{"id":5}</td></tr><tr><td>Eq</td><td>idEq</td><td>5</td><td>id = 5</td><td>{"idEq":5}</td></tr><tr><td>Not</td><td>idNot</td><td>5</td><td>id != 5</td><td>{"idNot":{"$ne":5}}</td></tr><tr><td>Ne</td><td>idNe</td><td>5</td><td>id &#x3C;> 5</td><td>{"idNe":{"$ne":5}}</td></tr><tr><td>Gt</td><td>idGt</td><td>5</td><td>id > 5</td><td>{"idGt":{"$gt":5}}</td></tr><tr><td>Ge</td><td>idGe</td><td>5</td><td>id >= 5</td><td>{"idGe":{"$gte":5}}</td></tr><tr><td>Lt</td><td>idLt</td><td>5</td><td>id &#x3C; 5</td><td>{"idLt":{"$lt":5}}</td></tr><tr><td>Le</td><td>idLe</td><td>5</td><td>id &#x3C;= 5</td><td>{"idLe":{"$lte":5}}</td></tr><tr><td>NotIn</td><td>idNotIn</td><td>[1,2,3]</td><td>id NOT IN (1,2,3)</td><td>{"id":{"$nin":[1, 2, 3]}}</td></tr><tr><td>In</td><td>idIn</td><td>[1,2,3]</td><td>id IN (1,2,3)</td><td>{"id":{"$in":[1, 2, 3]}}</td></tr><tr><td>Null</td><td>memoNull</td><td>false</td><td>memo IS NOT NULL</td><td>{"memo":{"$not":{"$type", 10}}}</td></tr><tr><td>Null</td><td>memoNull</td><td>true</td><td>memo IS NULL</td><td>{"memo":{"$type", 10}}</td></tr><tr><td>NotLike</td><td>nameNotLike</td><td>"arg"</td><td>name NOT LIKE '%arg%'</td><td>{"name":{"$not":{"$regex":"arg"}}}</td></tr><tr><td>Like</td><td>nameLike</td><td>"arg"</td><td>name LIKE '%arg%'</td><td>{"name":{"$regex":"arg"}}</td></tr><tr><td>NotStart</td><td>nameNotStart</td><td>"arg"</td><td>name NOT LIKE 'arg%'</td><td>{"name":{"$not":{"$regex":"^arg"}}}</td></tr><tr><td>Start</td><td>nameStart</td><td>"arg"</td><td>name LIKE 'arg%'</td><td>{"name":{"$regex":"^arg"}}</td></tr><tr><td>NotEnd</td><td>nameNotEnd</td><td>"arg"</td><td>name NOT LIKE '%arg'</td><td>{"name":{"$not":{"$regex":"arg$"}}}</td></tr><tr><td>End</td><td>nameEnd</td><td>"arg"</td><td>name LIKE '%arg'</td><td>{"name":{"$regex":"arg$"}}</td></tr><tr><td>NotContain</td><td>nameNotContain</td><td>"arg"</td><td>name NOT LIKE '%arg%’</td><td>{"name":{"$not":{"$regex":"arg"}}}</td></tr><tr><td>Contain</td><td>nameContain</td><td>"arg"</td><td>name LIKE '%arg%’</td><td>{"name":{"$regex":"arg"}}</td></tr><tr><td>Rx</td><td>nameRx</td><td>"arg\d"</td><td>name REGEXP 'arg\d’</td><td>{"name":{"$regex":"arg\d"}}</td></tr></tbody></table>
