# Predicate Suffix Fields

**Predicate suffix fields** are used to construct simple query conditions. The names of predicate suffix fields are defined through Attribute-Predicate Expressions (APE), i.e.:

  $$
  \{x \in \mathcal{R} : xP(v)\}
  $$
  
This is abbreviated as **xP**, where *x* represents an attribute in relation *R*, *P* is from a predefined set of predicates, and *v* denotes the query parameter.

### Predicate Suffix Mapping

DoytoQuery adopts the predicate suffix mapping method, which maps fields in the query object ending with predefined predicates to basic query conditions. Each basic query condition consists of a column name, a comparison operator, and a comparison value.

In a query object, the fields used to map basic query conditions are named in the format: column name + predicate alias. They are used to map the column name and comparison operator of the query condition, while the assigned value of the field serves as the comparison value for the query condition. In an instance of a query object, fields that have been assigned values are mapped to corresponding query conditions, which are then concatenated by the logical operator AND to form the query clause.

The following are two examples of suffix mapping:

```java
UserQuery userQuery = UserQuery.builder().deleted(true).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE deleted = ?" args="[true]"

UserQuery userQuery = UserQuery.builder().idIn(Arrays.asList(1, 4, 12)).deleted(true).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE id IN (?, ?, ?) AND deleted = ?" args="[1 4 12 true]"
```

### Predicate Suffix Table

The Predicate Suffix Table predefines the predicate suffixes supported in DoytoQuery and the query conditions to which they are mapped.

<table><thead><tr><th>谓词后缀</th><th>字段名称</th><th>赋值</th><th>SQL查询条件</th><th data-hidden>MongoDB Condition</th></tr></thead><tbody><tr><td>(EMPTY)</td><td>id</td><td>5</td><td>id = 5</td><td>{"id":5}</td></tr><tr><td>Eq</td><td>idEq</td><td>5</td><td>id = 5</td><td>{"idEq":5}</td></tr><tr><td>Not</td><td>idNot</td><td>5</td><td>id != 5</td><td>{"idNot":{"$ne":5}}</td></tr><tr><td>Ne</td><td>idNe</td><td>5</td><td>id &#x3C;> 5</td><td>{"idNe":{"$ne":5}}</td></tr><tr><td>Gt</td><td>idGt</td><td>5</td><td>id > 5</td><td>{"idGt":{"$gt":5}}</td></tr><tr><td>Ge</td><td>idGe</td><td>5</td><td>id >= 5</td><td>{"idGe":{"$gte":5}}</td></tr><tr><td>Lt</td><td>idLt</td><td>5</td><td>id &#x3C; 5</td><td>{"idLt":{"$lt":5}}</td></tr><tr><td>Le</td><td>idLe</td><td>5</td><td>id &#x3C;= 5</td><td>{"idLe":{"$lte":5}}</td></tr><tr><td>NotIn</td><td>idNotIn</td><td>[1,2,3]</td><td>id NOT IN (1,2,3)</td><td>{"id":{"$nin":[1, 2, 3]}}</td></tr><tr><td>In</td><td>idIn</td><td>[1,2,3]</td><td>id IN (1,2,3)</td><td>{"id":{"$in":[1, 2, 3]}}</td></tr><tr><td>Null</td><td>memoNull</td><td>true</td><td>memo IS NULL</td><td>{"memo":{"$type", 10}}</td></tr><tr><td>Null</td><td>memoNull</td><td>false</td><td>memo IS NOT NULL</td><td>{"memo":{"$not":{"$type", 10}}}</td></tr><tr><td>NotLike</td><td>nameNotLike</td><td>"arg"</td><td>name NOT LIKE '%arg%'</td><td>{"name":{"$not":{"$regex":"arg"}}}</td></tr><tr><td>Like</td><td>nameLike</td><td>"arg"</td><td>name LIKE '%arg%'</td><td>{"name":{"$regex":"arg"}}</td></tr><tr><td>NotStart</td><td>nameNotStart</td><td>"arg"</td><td>name NOT LIKE 'arg%'</td><td>{"name":{"$not":{"$regex":"^arg"}}}</td></tr><tr><td>Start</td><td>nameStart</td><td>"arg"</td><td>name LIKE 'arg%'</td><td>{"name":{"$regex":"^arg"}}</td></tr><tr><td>NotEnd</td><td>nameNotEnd</td><td>"arg"</td><td>name NOT LIKE '%arg'</td><td>{"name":{"$not":{"$regex":"arg$"}}}</td></tr><tr><td>End</td><td>nameEnd</td><td>"arg"</td><td>name LIKE '%arg'</td><td>{"name":{"$regex":"arg$"}}</td></tr><tr><td>NotContain</td><td>nameNotContain</td><td>"arg"</td><td>name NOT LIKE '%arg%’</td><td>{"name":{"$not":{"$regex":"arg"}}}</td></tr><tr><td>Contain</td><td>nameContain</td><td>"arg"</td><td>name LIKE '%arg%’</td><td>{"name":{"$regex":"arg"}}</td></tr><tr><td>Rx</td><td>nameRx</td><td>"arg\d"</td><td>name REGEXP 'arg\d’</td><td>{"name":{"$regex":"arg\d"}}</td></tr></tbody></table>
