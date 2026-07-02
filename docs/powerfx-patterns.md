# Power Fx Patterns

Use this file when the user needs practical Power Fx formulas or formula review. It complements [powerfx.md](powerfx.md), which explains core functions. Prefer these patterns over ad hoc formulas.

## Formula Answer Style

When answering a Power Fx question:

1. State assumptions: app type, data source, connector, control names, and column types.
2. Provide the formula.
3. Explain the key parts briefly.
4. Mention delegation, data-shape, error-handling, performance, and accessibility risks where relevant.
5. Ask for missing schema details if the formula depends on choice, person, lookup, or connector-specific behavior.

Avoid presenting a formula as universally correct when field types or data source are unknown.

## Gallery Filter Pattern

Use `With()` to normalize inputs once and keep the gallery formula readable.

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

Notes:

- Preserve the blank-search guard for optional search boxes.
- Verify delegation for the exact connector and column types.
- For SharePoint, index filter/sort columns for large-list performance, but do not treat indexing as proof of delegation.
- For Dataverse choices, the selected value shape may differ. Verify the combo box `Items` and selected record.

## Date Range Pattern

Avoid applying date functions to data-source columns in filters.

Bad:

```powerfx
Filter(Requests, Year(DueDate) = Year(Today()))
```

Better:

```powerfx
With(
    {
        startDate: Date(Year(Today()), 1, 1),
        endDate: Date(Year(Today()) + 1, 1, 1)
    },
    Filter(
        Requests,
        DueDate >= startDate &&
        DueDate < endDate
    )
)
```

The local boundary values are fine; the important improvement is avoiding `Year(DueDate)` on the data-source column.

## Validation Before Save

Use readable validation before `Patch()` or `SubmitForm()`.

```powerfx
With(
    {
        titleText: Trim(txtTitle.Text),
        hasDueDate: !IsBlank(dpDueDate.SelectedDate)
    },
    If(
        IsBlank(titleText),
        Notify("Title is required.", NotificationType.Error),
        !hasDueDate,
        Notify("Due date is required.", NotificationType.Error),
        SubmitForm(frmRequest)
    )
)
```

For important field errors, prefer inline messages near the fields as well as `Notify()`.

## Patch Save Pattern

Use `IfError()` around `Patch()` when saving through custom forms.

```powerfx
With(
    {
        titleText: Trim(txtTitle.Text),
        dueDateValue: dpDueDate.SelectedDate
    },
    If(
        IsBlank(titleText),
        Notify("Title is required.", NotificationType.Error),
        IfError(
            Patch(
                Requests,
                Defaults(Requests),
                {
                    Title: titleText,
                    DueDate: dueDateValue,
                    Status: { Value: "Open" }
                }
            ),
            Notify("The request could not be saved.", NotificationType.Error),
            Notify("Request saved.", NotificationType.Success)
        )
    )
)
```

The `Status` shape above is a common SharePoint choice pattern. Do not assume it applies to Dataverse choices.

## Form Save Pattern

Use forms when the screen maps cleanly to one record.

```powerfx
If(
    frmRequest.Valid,
    SubmitForm(frmRequest),
    Notify("Fix the highlighted fields before submitting.", NotificationType.Error)
)
```

Use form events:

```powerfx
// frmRequest.OnSuccess
Notify("Request saved.", NotificationType.Success);
Back()
```

```powerfx
// frmRequest.OnFailure
Notify("Request could not be saved: " & frmRequest.Error, NotificationType.Error)
```

## Variables and State

Use variables for state, not for every calculated value.

- Use `Set()` for app-wide state.
- Use `UpdateContext()` for screen-local state.
- Use collections for small temporary tables or selected items.
- Use named formulas or direct formulas for values that can be derived.

Good:

```powerfx
UpdateContext({ locShowConfirm: true })
```

Risky:

```powerfx
ClearCollect(colRequests, Requests)
```

Do not collect a large data source to hide delegation problems.

## ForAll Pattern

Use `ForAll()` for small sets of selected records or local collections. Do not use it as a bulk-update strategy for large remote data sets.

```powerfx
ForAll(
    colSelectedRequests As selectedRequest,
    IfError(
        Patch(
            Requests,
            selectedRequest,
            { Status: { Value: "Approved" } }
        ),
        Collect(colFailedUpdates, selectedRequest)
    )
)
```

Notes:

- `ForAll()` operates on the records available to the app.
- For large server-side operations, consider Power Automate, Dataverse, or a backend process.
- Verify field shapes before bulk updating choice, lookup, or person columns.

## Coalesce and Defaults

Use `Coalesce()` for simple fallback values.

```powerfx
Coalesce(txtDisplayName.Text, User().FullName, "Unknown user")
```

Use it for display text and default values, not to hide missing required data during save.

## Record Scope

Use `As` to make record scope clear, especially in nested formulas.

```powerfx
Filter(
    Requests As request,
    request.Status.Value = "Open" &&
    request.Requester.Email = User().Email
)
```

This improves readability and avoids ambiguity when multiple tables have similar field names.

## Component Property Pattern

For reusable components:

- Use input properties for labels, values, visibility, and configuration.
- Use output properties for selected values or validation state.
- Use behavior/event-style properties only when supported in the target environment.
- Keep data-source operations in the host screen unless the component is intentionally data-specific.

Example host usage:

```powerfx
cmpConfirm.Visible = locShowConfirmDelete
cmpConfirm.TitleText = "Delete request"
cmpConfirm.MessageText = "This action cannot be undone."
```

## Common Review Questions

When reviewing a formula, ask:

- What is the data source?
- What are the column types?
- Are any data-source columns wrapped in functions?
- Are choice/person/lookup fields using the correct shape?
- Could the formula miss data because of delegation?
- Is there a clearer `With()` structure?
- Is `IfError()` used for runtime save/flow failures?
- Are local variables used for state rather than avoidable copies of data?
