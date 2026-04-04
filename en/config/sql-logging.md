# SQL Logging

To view the executed SQL statements, simply configure the log level of `win.doyto.query.core.SqlAndArgs` to debug.

Configure as follows in a Spring YAML file:

{% code title="application.yml" %}
```yaml
logging:
  level:
    win.doyto.query.core.SqlAndArgs: debug
```
{% endcode %}

Log output is as follows:

```text
...
2021-02-25 22:17:18.442 DEBUG 80237 --- [           main] win.doyto.query.core.SqlAndArgs          : SQL  : SELECT platform, parentId, menuName, memo, valid, id, createUserId, createTime, updateUserId, updateTime FROM menu WHERE id = ?
2021-02-25 22:17:18.442 DEBUG 80237 --- [           main] win.doyto.query.core.SqlAndArgs          : Param: 3(java.lang.Integer)
2021-02-25 22:17:18.447 DEBUG 80237 --- [           main] win.doyto.query.core.SqlAndArgs          : SQL  : DELETE FROM menu WHERE id = ?
2021-02-25 22:17:18.447 DEBUG 80237 --- [           main] win.doyto.query.core.SqlAndArgs          : Param: 3(java.lang.Integer)
...
```



