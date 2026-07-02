# Delegation

Delegation means Power Apps sends a query to the data source so the server filters, sorts, searches, or looks up records before data reaches the app. Without delegation, Power Apps may only process the first configured page of records locally, which can produce incomplete results.

## Contents

- [Why Delegation Matters](#why-delegation-matters)
- [Delegation Language Discipline](#delegation-language-discipline)
- [SharePoint Common Limitations](#sharepoint-common-limitations)
- [Dataverse Advantages](#dataverse-advantages)
- [Excel Limitations](#excel-limitations)
- [Common Delegation Risks](#common-delegation-risks)
- [`Filter()`](#filter)
- [`LookUp()`](#lookup)
- [`Sort()` and `SortByColumns()`](#sort-and-sortbycolumns)
- [`Search()`, `StartsWith()`, and `In`](#search-startswith-and-in)
- [Rewriting Formulas](#rewriting-formulas)
- [When Uncertain](#when-uncertain)

## Why Delegation Matters

Delegation affects correctness, not just performance. A non-delegable filter can silently miss records beyond the local row limit.

The non-delegable row limit is an app setting. The common default is 500 records and it can often be raised to 2,000, but raising it does not fix correctness for larger data sets. Do not recommend setting this value above 2,000 or matching it to a list size such as 30,000. The right fix is usually to rewrite the formula or change the data source/design so the query can be delegated.

Do not describe non-delegable behavior as "loading all rows client-side" or "downloading all 30K rows." For a delegable data source with a non-delegable formula, Power Apps applies the non-delegable part only to the first configured row-limit records. That is why results can be incomplete.

Do not say "there is no partial delegation" or "the entire query falls back" as a universal rule. The practical warning is that if part of the formula cannot be delegated, Power Apps can return incomplete results because the non-delegable work is limited to the first configured row-limit records. Keep the explanation focused on correctness risk rather than oversimplifying the query planner.

Bad row-limit advice:

```text
Set the data row limit to 30,000.
```

Better:

```text
The non-delegable row limit is commonly 500 by default and maxes at 2,000. For 30,000 rows, the formula must delegate; raising the row limit cannot make a non-delegable query correct at that scale.
```

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

The examples in this section are reusable wording patterns. Do not treat them as only applying to one formula, list, or prompt. Apply the same principles to any connector: separate formula support from performance settings, identify uncertainty, and avoid turning heuristics into guarantees.

Avoid overconfident section headings:

- Avoid: `Delegation-Safe Formula`
- Prefer: `Safer SharePoint-Friendly Rewrite`
- Prefer: `Delegation-Friendly Rewrite to Verify`

Do not say:

```text
This will delegate if the columns are indexed.
```

Do not say:

```text
StartsWith(Title, searchText) delegates only if Title is indexed.
```

Do not say:

```text
SortByColumns delegates only if DueDate is indexed.
```

Say:

```text
This uses a more delegation-friendly formula shape. Verify that Power Apps Studio shows no delegation warning for this exact connector, column types, and formula. Indexing the SharePoint columns is still important for large-list performance and list-threshold behavior, but indexing does not make a non-delegable Power Fx pattern delegable.
```

For `StartsWith()` on SharePoint text columns, say:

```text
StartsWith(Title, searchText) is a SharePoint-friendly prefix-search pattern for text columns. Verify delegation in Power Apps Studio, and separately index Title if needed for large-list performance.
```

For sorting, say:

```text
SortByColumns by DueDate is a delegation-friendly sort shape for a date column, but verify in Studio for the exact connector and formula. Separately index DueDate if needed for SharePoint large-list performance.
```

Delegation support is about whether Power Apps can translate the formula to the connector query. SharePoint indexing is about whether SharePoint can efficiently evaluate large-list queries. They are related in practice, but they are not the same thing.

Do not call indexes "mandatory" unless the user gives a specific SharePoint threshold failure, tenant policy, or operational requirement. For general guidance, say indexes are strongly recommended for large lists and should be verified.

Do not recommend `IfError()` as a fix for delegation warnings. Delegation warnings are authoring-time signals; they normally do not throw runtime errors. Use `IfError()` for runtime operations such as `Patch()`, connector calls, and flow calls.

Do not recommend SharePoint calculated columns as a delegation workaround for search. For example, avoid suggesting `TitleLower = LOWER([Title])` in SharePoint and then filtering on it as if that solves delegation. If a normalized search value is needed, prefer a real text column populated by app logic, Power Automate, import/migration logic, or another controlled process, and still verify delegation. For true contains or full-text search at scale, consider Dataverse search, Microsoft Search/custom API, or another search-oriented service.

Avoid absolute wording such as "no delegable SharePoint solution exists" for contains search. Safer wording: standard SharePoint list filtering generally does not provide delegable contains search; for that requirement, use a search-oriented service or different data platform and verify current connector behavior.

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
With(
    { searchText: Trim(txtSearch.Text) },
    Filter(
        Requests,
        IsBlank(searchText) || StartsWith(Title, searchText)
    )
)
```

If uncertain, warn that delegation must be verified.

For SharePoint complex fields:

- Person columns: only some subfields, such as `Email` and `DisplayName`, are documented as delegable. Prefer `Email` for identity stability, but still verify. Do not call `DisplayName` absolutely non-delegable without checking current docs and the exact connector behavior.
- Choice and lookup columns: delegation depends on the subfield and operation. Do not assume `StartsWith()` on choice or lookup subfields is delegable.

Local values:

- `User().Email`, `User().FullName`, `Today()`, and `Year(Today())` are evaluated locally.
- Do not call those local values "non-delegable" by themselves.
- Prefer comparing data-source columns to local scalar values over wrapping data-source columns in functions. For example, `DueDate >= startDate` is safer than `Year(DueDate) = Year(Today())`.

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

Do not tie sort delegation to indexing. Sorting by a supported column can be a delegation-friendly formula shape; indexing the sort column is a separate SharePoint scale/performance recommendation.

## `Search()`, `StartsWith()`, and `In`

`Search()` and `in` are convenient but often risky depending on the connector. `StartsWith()` is often a safer option for prefix search, but still must be checked for the connector and column type.

Safer:

```powerfx
With(
    { searchText: Trim(txtSearch.Text) },
    Filter(
        Requests,
        IsBlank(searchText) || StartsWith(Title, searchText)
    )
)
```

When rewriting optional search, preserve the blank-search guard. A bare `StartsWith(Title, txtSearch.Text)` may be valid, but `IsBlank(searchText) || StartsWith(Title, searchText)` is clearer and more robust for user-controlled search inputs.

In full rewrites, bind the normalized search text in the same `With()` block as other constants:

```powerfx
With(
    {
        searchText: Trim(txtSearch.Text),
        startDate: Date(Year(Today()), 1, 1),
        endDate: Date(Year(Today()) + 1, 1, 1),
        userEmail: User().Email
    },
    Filter(
        Requests,
        IsBlank(searchText) || StartsWith(Title, searchText)
    )
)
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
