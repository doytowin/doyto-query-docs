# Subquery Field

For a general subquery,`score > (SELECT avg(score) FROM t_user WHERE deleted = ?)`, is divided into three parts in OQM for mapping:

* score >
* SELECT avg(score) FROM t\_user
* WHERE clause

The first part `score >`can be mapped using the field name `scoreGtXxx`, just like a normal predicate suffix field. The string defined after the predicate suffix is ​​only used to distinguish duplicate field names and will be ignored during mapping.

The second part contains one column name and a table name and won't change, therefore, GoooQo defines a tag `subquery` to save this information.

The second part contains a column and a table name, which are static and unchanging. GoooQo provides two tags to save this information: One is `subquery`, which is used to define the native subquery statement; The other is the combination of the tags `select` and `from`, which save the column name and table name respectively.

The third part is another WHERE clause, which can be mapped through a query object. Therefore, we define the field type as the corresponding query object and map the value to the WHERE clause in the subquery through the query object mapping method.

Here are some examples of subquery field definitions (since v0.2.0):

```go
ScoreLtAvg *UserQuery `subquery:"select avg(score) from User"`
ScoreLtAny *UserQuery `subquery:"SELECT score FROM User"`
ScoreLtAll *UserQuery `subquery:"select score from User"`
ScoreGtAvg *UserQuery `select:"avg(score)" from:"User"`
```
