# 谓词后缀字段

**谓词后缀字段**用于构建简单查询条件，谓词后缀字段的名称通过属性谓词表达式（Attribute-Predicate Expression，APE）进行定义，即:
  $$
  \{x \in \mathcal{R} : xP(v)\}
  $$
  简记为**xP**，其中*x*表示关系*R*中的属性，*P*来自于预定义的谓词集合，*v*表示查询参数。

### 谓词后缀映射

DoytoQuery采用谓词后缀映射方法，将查询对象中的字段中以预定义谓词结尾的字段映射为基本查询条件。每个基本查询条件由列名、比较运算符和比较值组成。

在查询对象中，用于映射基本查询条件的字段，命名格式为列名加谓词的别名，用于映射查询条件的列名和比较运算符，查询条件的比较值为字段的赋值。 一个查询对象实例中已赋值的字段会被映射为对应的查询条件，并由逻辑运算符AND拼接为查询子句。

以下为后缀映射的两个示例：

```java
UserQuery userQuery = UserQuery.builder().deleted(true).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE deleted = ?" args="[true]"

UserQuery userQuery = UserQuery.builder().idIn(Arrays.asList(1, 4, 12)).deleted(true).build();
List<UserEntity> users = userDataAccess.query(userQuery);
// SQL="SELECT id, name, score, memo, deleted FROM t_user WHERE id IN (?, ?, ?) AND deleted = ?" args="[1 4 12 true]"
```

### 谓词后缀表

谓词后缀表预定义了DoytoQuery中支持的谓词后缀以及映射的查询条件。

<table><thead><tr><th>谓词后缀</th><th>字段名称</th><th>赋值</th><th>SQL查询条件</th><th data-hidden>MongoDB Condition</th></tr></thead><tbody><tr><td>(EMPTY)</td><td>id</td><td>5</td><td>id = 5</td><td>{"id":5}</td></tr><tr><td>Eq</td><td>idEq</td><td>5</td><td>id = 5</td><td>{"idEq":5}</td></tr><tr><td>Not</td><td>idNot</td><td>5</td><td>id != 5</td><td>{"idNot":{"$ne":5}}</td></tr><tr><td>Ne</td><td>idNe</td><td>5</td><td>id &#x3C;> 5</td><td>{"idNe":{"$ne":5}}</td></tr><tr><td>Gt</td><td>idGt</td><td>5</td><td>id > 5</td><td>{"idGt":{"$gt":5}}</td></tr><tr><td>Ge</td><td>idGe</td><td>5</td><td>id >= 5</td><td>{"idGe":{"$gte":5}}</td></tr><tr><td>Lt</td><td>idLt</td><td>5</td><td>id &#x3C; 5</td><td>{"idLt":{"$lt":5}}</td></tr><tr><td>Le</td><td>idLe</td><td>5</td><td>id &#x3C;= 5</td><td>{"idLe":{"$lte":5}}</td></tr><tr><td>NotIn</td><td>idNotIn</td><td>[1,2,3]</td><td>id NOT IN (1,2,3)</td><td>{"id":{"$nin":[1, 2, 3]}}</td></tr><tr><td>In</td><td>idIn</td><td>[1,2,3]</td><td>id IN (1,2,3)</td><td>{"id":{"$in":[1, 2, 3]}}</td></tr><tr><td>Null</td><td>memoNull</td><td>true</td><td>memo IS NULL</td><td>{"memo":{"$type", 10}}</td></tr><tr><td>Null</td><td>memoNull</td><td>false</td><td>memo IS NOT NULL</td><td>{"memo":{"$not":{"$type", 10}}}</td></tr><tr><td>NotLike</td><td>nameNotLike</td><td>"arg"</td><td>name NOT LIKE '%arg%'</td><td>{"name":{"$not":{"$regex":"arg"}}}</td></tr><tr><td>Like</td><td>nameLike</td><td>"arg"</td><td>name LIKE '%arg%'</td><td>{"name":{"$regex":"arg"}}</td></tr><tr><td>NotStart</td><td>nameNotStart</td><td>"arg"</td><td>name NOT LIKE 'arg%'</td><td>{"name":{"$not":{"$regex":"^arg"}}}</td></tr><tr><td>Start</td><td>nameStart</td><td>"arg"</td><td>name LIKE 'arg%'</td><td>{"name":{"$regex":"^arg"}}</td></tr><tr><td>NotEnd</td><td>nameNotEnd</td><td>"arg"</td><td>name NOT LIKE '%arg'</td><td>{"name":{"$not":{"$regex":"arg$"}}}</td></tr><tr><td>End</td><td>nameEnd</td><td>"arg"</td><td>name LIKE '%arg'</td><td>{"name":{"$regex":"arg$"}}</td></tr><tr><td>NotContain</td><td>nameNotContain</td><td>"arg"</td><td>name NOT LIKE '%arg%’</td><td>{"name":{"$not":{"$regex":"arg"}}}</td></tr><tr><td>Contain</td><td>nameContain</td><td>"arg"</td><td>name LIKE '%arg%’</td><td>{"name":{"$regex":"arg"}}</td></tr><tr><td>Rx</td><td>nameRx</td><td>"arg\d"</td><td>name REGEXP 'arg\d’</td><td>{"name":{"$regex":"arg\d"}}</td></tr></tbody></table>
