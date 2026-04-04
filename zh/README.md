# 项目介绍

[![License](https://img.shields.io/:license-apache-brightgreen.svg)](https://www.apache.org/licenses/LICENSE-2.0.html) [![Maven Central](https://maven-badges.herokuapp.com/maven-central/win.doyto/doyto-query/badge.svg)](https://maven-badges.herokuapp.com/maven-central/win.doyto/doyto-query/) [![Sonar Stats](https://sonarcloud.io/api/project_badges/measure?project=win.doyto%3Adoyto-query\&metric=alert_status)](https://sonarcloud.io/dashboard?id=win.doyto%3Adoyto-query) [![Code Lines](https://sonarcloud.io/api/project_badges/measure?project=win.doyto%3Adoyto-query\&metric=ncloc)](https://sonarcloud.io/component_measures?id=win.doyto%3Adoyto-query\&metric=ncloc) [![Coverage Status](https://sonarcloud.io/api/project_badges/measure?project=win.doyto%3Adoyto-query\&metric=coverage)](https://sonarcloud.io/component_measures?id=win.doyto%3Adoyto-query\&metric=coverage)

DoytoQuery是一个基于对象查询映射（Object Query Mapping，OQM）技术实现的一款Java版数据库访问框架。其核心思想是完全通过对象构建查询语句，从而不再需要编写构建查询语句的代码。

所述对象包含以下几类：

* **查询对象（Query Object）** 用于动态构建WHERE子句，通过字段构建查询条件，并根据字段的赋值动态组合查询条件。
* **实体对象（Entity Object）** 用于映射单表查询语句中的表名和列名。实体对象的实例用于映射目标表的行数据。
* **分页查询对象（PageQuery）** 定义了分页和排序字段，用于接收前端传递的参数并生成相应的分页和排序子句。作为所有查询对象的基类，它为所有查询接口提供了分页和排序功能。
* **视图对象（View Object）** 用于映射包含查询对象的复杂查询语句。由于复杂查询语句通常包含聚合列和多表关联，因此我们需要一种新的对象类型来替代实体对象。
* **聚合查询对象（Having Object）** 是对聚合后的记录进行过滤的查询对象，用于映射HAVING子句，继承自基础的查询对象。查询对象中定义的字段用于构建`WHERE`子句的条件，而聚合查询对象中定义的字段则用于构建`HAVING`子句的条件。
