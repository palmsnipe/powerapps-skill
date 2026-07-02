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
Filter(
    Requests,
    IsBlank(txtSearch.Text) || StartsWith(Title, txtSearch.Text)
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

## SharePoint-Safe Version

Assume:

- `Status` is a SharePoint choice column.
- `Priority` is a SharePoint choice column.
- `Title` is indexed if used for filtering.
- `Status` and `Priority` are indexed where practical.

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

Delegation warning: SharePoint support depends on column types and formula shape. Avoid `in`, `Lower(Title)`, calculated columns, and complex expressions in the predicate for large lists.

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
- SharePoint lists need indexed columns for large-list filtering.
- Excel is not a good backend for large searchable data.
