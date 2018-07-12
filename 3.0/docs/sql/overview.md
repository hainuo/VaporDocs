# Using SQL

The SQL library helps you build and serialize SQL queries in Swift. It has an extensible, protocol-based design that supports many standard SQL queries like:

- `SELECT`, `INSERT`, `UPDATE`, `DELETE`
- `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`
- `CREATE INDEX`, `DROP INDEX`

This package also integrates deeply with `Codable` and parameter binding to make working with your database fast and secure.

Vapor's SQL database packages ([PostgreSQL](../postgresql/getting-started.md), [MySQL](../mysql/getting-started.md), and [SQLite](../sqlite/getting-started.md)) are built on top of this library. In some cases, these SQL dialects will have different syntaxes or supported features. Be sure to check their API docs for additional functionality. 

## Connection

The first step to building a SQL query is getting access to a connection. Most often, you will use `withPooledConnection(to:)` followed by your database's `dbid`. 

```swift
router.get("sql") { req in
    return req.withPooledConnection(to: .<#dbid#>) { conn in
        return // use conn to perform a query
    }
}
```

Check out [Database Kit &rarr; Overview &rarr; Connections](../database-kit/overview.md/#connections) for more information. The rest of this guide will assume you have access to a SQL database connection.

## Select

Use the [`select()`](#fixme) method on a connection to create a [`SQLSelectBuilder`](#fixme). This builder helps you create `SELECT` statements and supports:

- `*`, columns, and expressions like functions
- `FROM`
- `JOIN`
- `GROUP BY`
- `ORDER BY`

The select builder conforms to [`SQLPredicateBuilder`](#fixme) for building `WHERE` predicates. It also conforms to [`SQLQueryFetcher`](#fixme) for decoding `Codable` models from the result set.

Let's take a look at an example `SELECT` query. Replace the Xcode placeholder with the name of the database you are using, i.e., `SQLite`. 

```swift
struct User: SQLTable, Codable {
    static let sqlTableIdentifierString = "users"
    let id: Int?
    let name: String
}

let users = conn.select()
    .all().from(User.self)
    .where(\User.name == "Vapor")
    .all(decoding: User.self)
print(users) // Future<[User]>
```

The resulting SQL will look something like this:

```sql
SELECT * FROM "users" WHERE "users"."name" = ?
```

As you can see, the Swift code reads similarly to actual SQL. Be sure to visit the API docs for the various builder protocols to see all available methods.

## API Docs

Check out the [API docs](https://api.vapor.codes/sql/latest/SQL/index.html) for more in-depth information about SQL's APIs.