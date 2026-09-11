# IEnumerable vs IQueryable

## Interview Question

What is the difference between IEnumerable and IQueryable?

## Short Answer

IEnumerable executes LINQ operations against in-memory data,
while IQueryable allows the query provider to translate the
expression into a query such as SQL.

## Senior-Level Thinking

The important difference is not simply "memory vs database".

The real question is:

> Where does the work execute?

## Example

```csharp
var users = db.Users
    .Where(x => x.IsActive);