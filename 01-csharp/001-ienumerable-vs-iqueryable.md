# IEnumerable vs IQueryable

## Interview Question

> What is the difference between `IEnumerable` and `IQueryable`?

## Weak Answer

> `IEnumerable` works in memory, while `IQueryable` works with the database.

This is not completely wrong.

But it's not enough for a Senior-level interview.

## Strong Answer

`IEnumerable` represents a sequence that is typically processed by the .NET runtime, while `IQueryable` represents a query that can be translated by a query provider, such as EF Core, into a query executed by the underlying data source.

The important difference is:

> **Where does the work execute?**

With `IQueryable`, EF Core can translate the expression into SQL and let the database perform filtering, sorting, projection, and other operations.

With `IEnumerable`, LINQ operations are performed by .NET against the objects being enumerated.

## Example

```csharp
var users = db.Users
    .Where(x => x.IsActive);
```

EF Core can translate this into SQL similar to:

```sql
SELECT *
FROM Users
WHERE IsActive = 1;
```

The database performs the filtering.

Compare that with:

```csharp
var users = db.Users
    .AsEnumerable()
    .Where(x => x.IsActive);
```

`AsEnumerable()` changes the execution boundary.

The filtering now happens through LINQ-to-Objects after the sequence has crossed into the application.

## Why Does This Matter?

Imagine the database contains:

```text
1,000,000 users
```

If filtering happens in the database:

```text
Database
   ↓
WHERE IsActive = 1
   ↓
Only matching rows
   ↓
Application
```

If filtering happens in memory:

```text
Database
   ↓
Large result set
   ↓
Application memory
   ↓
WHERE IsActive = 1
```

The second approach can create unnecessary network traffic, memory usage, and application work.

## Senior-Level Thinking

The interview isn't really testing whether you memorized:

> `IEnumerable` vs `IQueryable`

It's testing whether you understand:

* Deferred execution
* Query providers
* LINQ-to-Objects
* SQL translation
* Database vs application responsibilities
* Performance implications

## Common Mistake

Don't blindly assume that `IQueryable` is always better.

A query can still be inefficient because of:

* Poor SQL translation
* Missing indexes
* N+1 queries
* Loading unnecessary columns
* Complex expressions
* Large result sets

The real skill is understanding **where the work happens and whether that location makes sense**.

## Interview Follow-Up Questions

An interviewer may continue with:

* What is deferred execution?
* What happens when you call `ToList()`?
* What does `AsEnumerable()` do?
* When should you use `AsNoTracking()`?
* How does EF Core translate LINQ into SQL?
* How would you diagnose a slow EF Core query?

These follow-up questions are where the interview usually gets interesting.
