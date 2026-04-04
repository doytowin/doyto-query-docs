# View Object

Complex queries often involve joins across multiple tables and aggregation operations on columns. While query objects can be reused to construct the `WHERE` clause, entity objects cannot fully express the other parts of a query. Therefore, we introduce a dedicated **view object** to define the static parts of a complex query, along with a separate **Having object** to generate the `HAVING` clause. Additionally, the view object serves as the target for result mapping.

To achieve automatic mapping for complex queries, we divide the mapping process into the following three parts:

1. **Column Mapping**: The fields defined in the view object are used to map the required columns in the query results, including both regular columns and aggregated columns.
2. **Join Mapping**: Annotations within the view object define the relationships between tables, supporting the automatic generation of necessary join statements.
3. **HAVING Clause Mapping**: The Having object is used to generate the `HAVING` clause, supporting filtering conditions based on aggregated results.

This design not only improves developers' efficiency in constructing and maintaining complex query statements but also reduces the risks associated with manually assembling SQL. It significantly enhances the readability, reusability, and maintainability of the code.

## Aggregate Query Interface

In addition to designing interfaces for single-table data access, a separate interface is required for aggregate queries.

The interface `AggregateClient` has been designed and implemented, defined as follows:
This interface provides a general-purpose method `query`, allowing developers to perform aggregate queries by specifying the target view class and a query object, with the results mapped to a list of the corresponding view objects.

```java
public interface AggregateClient {
    <V> AggregateChain<V> aggregate(Class<V> viewClass);

    default <V> List<V> query(Class<V> viewClass, DoytoQuery query) {
        return aggregate(viewClass).filter(query).query();
    }

    default <V> long count(Class<V> viewClass, DoytoQuery query) {
        return aggregate(viewClass).filter(query).count();
    }

    default <V> PageList<V> page(Class<V> viewClass, DoytoQuery query) {
        return aggregate(viewClass).filter(query).page();
    }
}
```

