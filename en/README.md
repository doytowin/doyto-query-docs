---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Introduction

DoytoQuery is a Java-based database access framework built on Object Query Mapping (OQM) technology. Its core idea is to generate query statements from a set of objects:

* **Query Object**: Used to dynamically construct the `WHERE` clause by building query conditions based on fields and combining them dynamically according to their assigned values.
* **Entity Object**: Used to map table names and column names in single-table queries. Instances of entity objects represent rows in the target table.
* **PageQuery**: Defines pagination and sorting fields, serving to receive parameters from the frontend and generate the corresponding pagination and sorting clauses. As the base class for all query objects, it provides pagination and sorting capabilities for all query interfaces.
* **View Object**: Used to map complex query statements that include query objects. Since complex queries often involve aggregate columns and multi-table joins, a new type of object is needed to replace the entity object.
* **Having Object**: A specialized query object used to filter aggregated records by mapping to the `HAVING` clause. It inherits from the base query object. Fields defined in the query object are used to construct conditions for the `WHERE` clause, while fields in the having object are used for the `HAVING` clause.

