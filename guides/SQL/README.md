# SQL

Starcounter SQL follows the established standard SQL92 (ANSI INCITS 135-1992, R1998) to support easy data exchange with other databases and external tools. Currently we only support the query part of SQL92 which consists of the `SELECT` statement. See [Limitations](/guides/SQL/limitations).

## Calling SQL

There are currently two main ways of using Starcounter SQL:
1. Writing the queries in the programming code with `Db.SQL` as described in [Querying using SQL](/guides/database/querying-using-sql).
2. Executing queries in the interactive SQL browser of the [Starcounter Administrator](/guides/working-with-starcounter/administrator-web-ui).

The SQL in the Administrator and in code are not identical. For performance reasons, [literals](literals) can't be used in the programming code with `Db.SQL` while it's available in the Administrator. Because of this, when using the examples in this documentation, remember to replace literals with [variables](/guides/database/variables/) in the programming code.

## Object extensions to SQL

Starcounter SQL contains some extensions to the SQL92 standard to better deal with objects, since the standard SQL only supports relational databases. For these extensions we follow the Object Data Standard ODMG 3.0 (ISBN 1-55860-647-4). The object extensions in Starcounter SQL are:

- object references,
- [path expressions](/guides/SQL/path-expressions).

In traditional SQL you can only refer to tables, columns, rows and fields of values. The concept of an "object" is represented by a row in a table. You refer to an "object" by values on its primary key which are some specified columns.

In Starcounter SQL you can refer to an object itself. For example, in query below the identifier `e` is an object reference.

```sql
SELECT e FROM Employee e
```

In a traditional SQL database, to get data from more than one type of object (table/class) you have to do a "join" of a number of tables. For example, query below gives you the names of the employees and the names of the departments where they work.

```sql
SELECT e.FirstName, d.Name FROM Employee e JOIN Department d ON e.DepartmentId = d.Id
```

In Starcounter SQL there is a more convenient way to get the same result by instead using a path expression as in query below. In that way, you can from one type of object (`Employee`) reach another type of object (`Department`) by object reference.

```sql
SELECT e.FirstName, e.Department.Name FROM Employee e
```

In object oriented programming the extent of a class is all object instances of that class. An extent of object instances corresponds to a table of rows in a relational database. Thus, `Employee` in the example queries above can either be regarded as the extent of the class `Employee` or as the table `Employee`.

## Performance notes

The current version of Starcounter SQL is not performance optimized. However, for most queries with the right indexes, Starcounter SQL gives you the extreme performance of the Starcounter database.

{% import "../../macros.html" as macros %}

{{ macros.tocGenerator(page.title, summary.parts[0].articles[4].articles[1].articles) }}
