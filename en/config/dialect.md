# Dialect

### Add dependency

```xml
<dependency>
    <groupId>win.doyto</groupId>
    <artifactId>doyto-query-dialect</artifactId>
    <version>${doyto-query.version}</version>
</dependency>
```

### Configuration

#### File Configuration

```yml
doyto:
  query:
    config:
      dialect: win.doyto.query.dialect.PostgreSQLDialect
```
#### Static Method Configuration

```java
GlobalConfiguration.instance().setDialect(new HSQLDBDialect());
```

### Supported Databases

| Database   | Dialect                                   |
|------------|-------------------------------------------|
| HSQLDB     | win.doyto.query.dialect.HSQLDBDialect     |
| MySQL 5    | win.doyto.query.dialect.MySQLDialect      |
| MySQL 8    | win.doyto.query.dialect.MySQL8Dialect     |
| Oracle     | win.doyto.query.dialect.OracleDialect     |
| PostgreSQL | win.doyto.query.dialect.PostgreSQLDialect |
| SQL Server | win.doyto.query.dialect.SQLServerDialect  |
| SQLite     | win.doyto.query.dialect.SQLiteDialect     |


### Interface Design

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
