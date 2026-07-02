# Gallery Filter Examples

Gallery filtering must be designed around the data source. A formula that works on a small collection may miss data when used against SharePoint, Excel, or another connector with delegation limits.

## Simple Filter

Gallery `Items`:

```powerfx
Filter(Requests, Status = "Open")
```

For SharePoint choice columns:

```powerfx
Filter(Requests, Status.Value = "Open")
```

## Search Box with `StartsWith()`

Use `StartsWith()` for prefix search when supported by the connector.

```powerfx
With(
    { searchText: Trim(txtSearch.Text) },
    Filter(
        Requests,
        IsBlank(searchText) || StartsWith(Title, searchText)
    )
)
```

Delegation note: confirm `StartsWith()` support for the connector and column type. This is often safer than `in` or `Search()` for SharePoint-style lists, but do not assume.

## Dropdown Filter

```powerfx
Filter(
    Requests,
    IsBlank(ddStatus.Selected.Value) || Status = ddStatus.Selected.Value
)
```

SharePoint choice version:

```powerfx
Filter(
    Requests,
    IsBlank(ddStatus.Selected.Value) || Status.Value = ddStatus.Selected.Value
)
```

## Multiple Filters Using `With()`

```powerfx
With(
    {
        searchText: Trim(txtSearch.Text),
        selectedStatus: ddStatus.Selected.Value,
        selectedPriority: ddPriority.Selected.Value
    },
    Filter(
        Requests,
        (IsBlank(searchText) || StartsWith(Title, searchText)) &&
        (IsBlank(selectedStatus) || Status = selectedStatus) &&
        (IsBlank(selectedPriority) || Priority = selectedPriority)
    )
)
```

This keeps the formula readable and avoids repeating control references.

## SharePoint-Friendly Version to Verify

Assume:

- `Status` is a SharePoint choice column.
- `Priority` is a SharePoint choice column.
- `Title` is a standard text column.
- `Status` and `Priority` use choice values whose `.Value` subfield is being compared with equality.
- Filter and sort columns are indexed where practical for SharePoint large-list performance.

```powerfx
With(
    {
        searchText: Trim(txtSearch.Text),
        selectedStatus: ddStatus.Selected.Value,
        selectedPriority: ddPriority.Selected.Value
    },
    SortByColumns(
        Filter(
            Requests,
            (IsBlank(searchText) || StartsWith(Title, searchText)) &&
            (IsBlank(selectedStatus) || Status.Value = selectedStatus) &&
            (IsBlank(selectedPriority) || Priority.Value = selectedPriority)
        ),
        "Created",
        SortOrder.Descending
    )
)
```

Delegation warning: SharePoint support depends on column types and formula shape. Avoid `in`, `Lower(Title)`, calculated columns, and complex expressions in the predicate for large lists. Indexing helps SharePoint execute large-list queries, but it does not make non-delegable Power Fx patterns delegable.

## Dataverse Version

Assume:

- `Requests` is a Dataverse table.
- `Status` and `Priority` are Dataverse choices, and the combo boxes return compatible choice values.
- `Name` is the primary name column.

```powerfx
With(
    {
        searchText: Trim(txtSearch.Text),
        selectedStatus: cmbStatus.Selected.Value,
        selectedPriority: cmbPriority.Selected.Value
    },
    SortByColumns(
        Filter(
            Requests,
            (IsBlank(searchText) || StartsWith(Name, searchText)) &&
            (IsBlank(selectedStatus) || Status = selectedStatus) &&
            (IsBlank(selectedPriority) || Priority = selectedPriority)
        ),
        "createdon",
        SortOrder.Descending
    )
)
```

Dataverse usually supports more delegable queries than SharePoint, but still verify choices, column names, and formula delegation in the app.

## Delegation Warnings to Include

When giving gallery filter advice, mention:

- The data source determines delegation.
- Search-like formulas are common delegation risks.
- `in`, `Search()`, and functions around columns are risky for large data.
- SharePoint lists need delegation-friendly formulas and indexed filter/sort columns for large-list scale.
- Do not claim "will delegate if indexed"; say "verify in Power Apps Studio."
- Do not claim "`StartsWith` delegates only if the column is indexed"; say indexing is separate from delegation and helps with large-list performance.
- Do not claim sorting delegates only if the sort column is indexed; keep sort delegation verification separate from indexing advice.
- Do not suggest SharePoint calculated columns such as `TitleLower = LOWER([Title])` as delegation workarounds. If normalized search is needed, use a real maintained text column or a search-capable data service and still verify delegation.
- Excel is not a good backend for large searchable data.
