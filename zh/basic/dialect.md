# 数据库方言

### 添加依赖

```xml
<dependency>
    <groupId>win.doyto</groupId>
    <artifactId>doyto-query-dialect</artifactId>
    <version>${doyto-query.version}</version>
</dependency>
```

### 配置方式

#### 方式一: 文件配置

```yml
doyto:
  query:
    config:
      dialect: win.doyto.query.dialect.PostgreSQLDialect
```
#### 方式二：静态方法设置

```java
GlobalConfiguration.instance().setDialect(new HSQLDBDialect());
```

### 数据库支持

| Database   | Dialect                                   |
|------------|-------------------------------------------|
| HSQLDB     | win.doyto.query.dialect.HSQLDBDialect     |
| MySQL 5    | win.doyto.query.dialect.MySQLDialect      |
| MySQL 8    | win.doyto.query.dialect.MySQL8Dialect     |
| Oracle     | win.doyto.query.dialect.OracleDialect     |
| PostgreSQL | win.doyto.query.dialect.PostgreSQLDialect |
| SQL Server | win.doyto.query.dialect.SQLServerDialect  |
| SQLite     | win.doyto.query.dialect.SQLiteDialect     |


### 接口说明

{% code title="Dialect.java" %}
```java
package win.doyto.query.core;

public interface Dialect {
    String buildPageSql(String sql, int limit, long offset);
    default String wrapLabel(String fieldName) {
        return fieldName;
    }
    // Other methods..
}
```
{% endcode %}
