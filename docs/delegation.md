# Delegation

Delegation means Power Apps sends a query to the data source so the server filters, sorts, searches, or looks up records before data reaches the app. Without delegation, Power Apps may only process the first configured page of records locally, which can produce incomplete results.

## Why Delegation Matters

Delegation affects correctness, not just performance. A non-delegable filter can silently miss records beyond the local row limit.

The non-delegable row limit is an app setting. The common default is 500 records and it can often be raised to 2,000, but raising it does not fix correctness for larger data sets. The right fix is usually to rewrite the formula or change the data source/design so the query can be delegated.

Do not describe non-delegable behavior as "loading all rows client-side" or "downloading all 30K rows." For a delegable data source with a non-delegable formula, Power Apps applies the non-delegable part only to the first configured row-limit records. That is why results can be incomplete.

Always mention delegation when discussing:

- `Filter()`
- `LookUp()`
- `Sort()` or `SortByColumns()`
- `Search()`
- Aggregations such as counts and sums
- `StartsWith()`
- `in`
- Complex predicates

## Delegation Language Discipline

Be careful with certainty. Prefer "likely delegable" or "SharePoint-friendly formula shape; verify in Power Apps Studio" over absolute green-check claims.

Avoid overconfident section headings:

- Avoid: `Delegation-Safe Formula`
- Prefer: `Safer SharePoint-Friendly Rewrite`
- Prefer: `Delegation-Friendly Rewrite to Verify`

Do not say:

```text
This will delegate if the columns are indexed.
```

Say:

```text
This uses a more delegation-friendly formula shape. Verify that Power Apps Studio shows no delegation warning for this exact connector, column types, and formula. Indexing the SharePoint columns is still important for large-list performance and list-threshold behavior, but indexing does not make a non-delegable Power Fx pattern delegable.
```

Delegation support is about whether Power Apps can translate the formula to the connector query. SharePoint indexing is about whether SharePoint can efficiently evaluate large-list queries. They are related in practice, but they are not the same thing.

Do not call indexes "mandatory" unless the user gives a specific SharePoint threshold failure, tenant policy, or operational requirement. For general guidance, say indexes are strongly recommended for large lists and should be verified.

Do not recommend `IfError()` as a fix for delegation warnings. Delegation warnings are authoring-time signals; they normally do not throw runtime errors. Use `IfError()` for runtime operations such as `Patch()`, connector calls, and flow calls.

## SharePoint Common Limitations

SharePoint is common but has important limits:

- Many text search patterns are not delegable.
- `Search()` is often risky.
- `in` is commonly non-delegable.
- Calculated columns and complex expressions can break delegation.
- Large lists need simple delegation-friendly predicates, plus indexed filter/sort columns for SharePoint scale.
- Choice, person, and lookup columns require careful formula shape.

Prefer simple filters on appropriate columns, and index those columns for large SharePoint lists:

```powerfx
Filter(Requests, Status.Value = "Open")
```

For search-like behavior, prefer `StartsWith()` where supported:

```powerfx
Filter(Requests, StartsWith(Title, txtSearch.Text))
```

If uncertain, warn that delegation must be verified.

For SharePoint complex fields:

- Person columns: only some subfields, such as `Email` and `DisplayName`, are documented as delegable. Prefer `Email` for stability, but still verify.
- Choice and lookup columns: delegation depends on the subfield and operation. Do not assume `StartsWith()` on choice or lookup subfields is delegable.

## Dataverse Advantages

Dataverse generally supports more delegable operations than SharePoint and is better suited for relational business apps. It provides:

- Tables with typed columns.
- Relationships and lookups.
- Security roles.
- Better solution support.
- Better delegation and query behavior for many scenarios.

Still, do not assume every formula is delegable. Formula shape and column type matter.

## Excel Limitations

Excel is best for small, simple data sets. It is not a strong backend for production apps with large tables, concurrent users, security requirements, relational data, or robust delegation needs.

For larger or business-critical apps, prefer Dataverse or another proper data source.

## Common Delegation Risks

Risky patterns include:

```powerfx
Filter(Requests, Lower(Title) = Lower(txtSearch.Text))
```

```powerfx
Filter(Requests, txtSearch.Text in Title)
```

```powerfx
Filter(Requests, DateDiff(Created, Today()) < 30)
```

```powerfx
Sort(Filter(Requests, Status = "Open"), Len(Title))
```

These formulas compute values in the predicate or sort expression, which commonly prevents server-side processing.

## `Filter()`

Prefer simple column comparisons:

```powerfx
Filter(Requests, Status = "Open" && Priority = "High")
```

Avoid wrapping columns in functions inside the predicate when working with large data.

## `LookUp()`

Use `LookUp()` for a single known match:

```powerfx
LookUp(Employees, Email = User().Email)
```

Avoid complex transformations:

```powerfx
// Risky
LookUp(Employees, Lower(Email) = Lower(User().Email))
```

## `Sort()` and `SortByColumns()`

Prefer `SortByColumns()` with a real column:

```powerfx
SortByColumns(Requests, "Created", SortOrder.Descending)
```

Avoid sorting by computed expressions when data volume matters.

## `Search()`, `StartsWith()`, and `In`

`Search()` and `in` are convenient but often risky depending on the connector. `StartsWith()` is often a safer option for prefix search, but still must be checked for the connector and column type.

Safer:

```powerfx
Filter(Requests, StartsWith(Title, txtSearch.Text))
```

Risky:

```powerfx
Filter(Requests, txtSearch.Text in Title)
```

## Rewriting Formulas

Instead of:

```powerfx
Filter(Requests, Year(Created) = Year(Today()))
```

Use date boundaries:

```powerfx
With(
    {
        startDate: Date(Year(Today()), 1, 1),
        endDate: Date(Year(Today()) + 1, 1, 1)
    },
    Filter(Requests, Created >= startDate && Created < endDate)
)
```

Instead of:

```powerfx
Filter(Requests, Lower(Status) = "open")
```

Use normalized data or exact stored values:

```powerfx
Filter(Requests, Status = "Open")
```

## When Uncertain

Say delegation cannot be confirmed without the connector, column types, formula, and app row limit. Provide a safer shape and tell the user what to verify in Power Apps Studio and current Microsoft Learn delegation documentation.

Good uncertainty wording:

```text
This is a safer SharePoint-friendly rewrite, but I would not call it guaranteed delegable until Studio shows no delegation warning for the exact formula and list schema.
```

Good testing wording:

```text
Do not rely only on Gallery.AllItems or a visible count. Test with known records beyond the first 500/2,000 that should match each filter, and confirm they appear when the filter is applied.
```
