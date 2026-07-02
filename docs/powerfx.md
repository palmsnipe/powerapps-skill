# Power Fx Practical Guidance

Power Fx formulas should be readable, predictable, and source-aware. Favor formulas that are easy to debug and maintain over clever one-liners.

## `With()`

Use `With()` to name intermediate values inside a formula. This avoids repeating expressions and makes formulas easier to read.

Good:

```powerfx
With(
    {
        selectedCustomer: galCustomers.Selected,
        requestedAmount: Value(txtAmount.Text)
    },
    If(
        requestedAmount <= selectedCustomer.CreditLimit,
        Patch(
            Orders,
            Defaults(Orders),
            {
                Customer: selectedCustomer,
                Amount: requestedAmount
            }
        ),
        Notify("Amount exceeds credit limit.", NotificationType.Error)
    )
)
```

Bad:

```powerfx
If(
    Value(txtAmount.Text) <= galCustomers.Selected.CreditLimit,
    Patch(Orders, Defaults(Orders), { Customer: galCustomers.Selected, Amount: Value(txtAmount.Text) })
)
```

## Variables and Collections

Use variables for UI state and values that truly need to be stored.

- `Set()` creates or updates a global variable. Use for app-wide state, such as the current user profile or selected theme.
- `UpdateContext()` creates or updates a screen context variable. Use for screen-local state, such as dialog visibility.
- Collections store in-memory tables. Use them for small local working sets, temporary selections, or offline-style staging.

Avoid copying large server tables into collections just to avoid delegation. That usually hides the delegation problem and creates stale data.

Good:

```powerfx
UpdateContext({ locShowConfirmDelete: true })
```

Risky:

```powerfx
ClearCollect(colAllRequests, Requests)
```

This may only collect the first page of data depending on app settings and connector behavior.

## `Patch()`

Use `Patch()` for custom save flows, partial updates, or when a form control is not the right fit.

New record:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Title: txtTitle.Text,
        Status: "New"
    }
)
```

Existing record:

```powerfx
Patch(
    Requests,
    galRequests.Selected,
    {
        Status: "Approved",
        ApprovedOn: Now()
    }
)
```

Wrap important saves in `IfError()` and show clear feedback.

```powerfx
IfError(
    Patch(
        Requests,
        galRequests.Selected,
        { Status: "Approved" }
    ),
    Notify("The request could not be saved.", NotificationType.Error),
    Notify("Request saved.", NotificationType.Success)
)
```

## `SubmitForm()`

Use `SubmitForm()` when using an Edit form with cards and built-in validation.

```powerfx
SubmitForm(frmRequest)
```

Use the form's `OnSuccess` and `OnFailure` properties:

```powerfx
// frmRequest.OnSuccess
Notify("Saved.", NotificationType.Success);
Back()
```

```powerfx
// frmRequest.OnFailure
Notify("Save failed: " & frmRequest.Error, NotificationType.Error)
```

## `LookUp()`

`LookUp()` returns the first record matching a condition. It is useful for finding a single record but can have delegation limits depending on the condition and connector.

```powerfx
LookUp(Employees, Email = User().Email)
```

Avoid using non-delegable calculations inside the predicate:

```powerfx
// Risky
LookUp(Employees, Lower(Email) = Lower(User().Email))
```

Prefer storing normalized values if case-insensitive matching is required at scale.

## `Filter()`

Use `Filter()` for table filtering. Delegation depends on the connector and operators used.

Good candidate:

```powerfx
Filter(Requests, Status = "Open")
```

Risky:

```powerfx
Filter(Requests, "urgent" in Lower(Title))
```

The `in` operator and functions inside predicates are common delegation risks. Confirm connector support.

## `SortByColumns()`

Use `SortByColumns()` for explicit column-based sorting.

```powerfx
SortByColumns(
    Filter(Requests, Status = "Open"),
    "Created",
    SortOrder.Descending
)
```

Sorting is connector-specific. Sorting by simple columns is often safer than sorting by computed expressions.

## `Search()`

`Search()` is convenient but not universally delegable. It is often risky with SharePoint and Excel.

```powerfx
Search(Requests, txtSearch.Text, Title)
```

Current Power Fx syntax uses column identifiers for `Search()` columns. Older apps may show quoted logical column names, especially in formulas created before the newer syntax migration.

For larger lists, prefer a delegable pattern such as `StartsWith()` when supported by the connector:

```powerfx
Filter(Requests, StartsWith(Title, txtSearch.Text))
```

## `ForAll()`

Use `ForAll()` for record-by-record operations on small tables. Avoid using it as a bulk server update strategy for large data sets.

```powerfx
ForAll(
    colSelectedRequests As selectedRequest,
    Patch(
        Requests,
        selectedRequest,
        { Status: "Approved" }
    )
)
```

`ForAll()` is not a delegation escape hatch. It operates over the records available to the app.

## `Concurrent()`

Use `Concurrent()` to run independent operations at the same time, often during startup or refresh.

```powerfx
Concurrent(
    Refresh(Requests),
    Refresh(Departments),
    Refresh(Priorities)
)
```

Do not use `Concurrent()` when one operation depends on the result of another.

## `Coalesce()`

Use `Coalesce()` to choose the first non-blank value.

```powerfx
Coalesce(txtDisplayName.Text, User().FullName, "Unknown user")
```

This is cleaner than nested `If(IsBlank(...))` checks.

## `IfError()`

Use `IfError()` around operations that can fail, such as `Patch()` and flow calls.

```powerfx
IfError(
    MyFlow.Run(txtRequestId.Text),
    Notify("Flow failed.", NotificationType.Error),
    Notify("Flow started.", NotificationType.Success)
)
```

## `IsBlank()` vs `IsEmpty()`

- `IsBlank(value)` checks whether a scalar value is blank.
- `IsEmpty(table)` checks whether a table has no records.

Good:

```powerfx
If(IsBlank(txtTitle.Text), Notify("Title is required.", NotificationType.Error))
```

```powerfx
If(IsEmpty(colSelectedItems), Notify("Select at least one item.", NotificationType.Warning))
```

Bad:

```powerfx
If(IsEmpty(txtTitle.Text), Notify("Title is required."))
```

## Record Scope, `ThisRecord`, and `As`

Use `As` to make record scope explicit, especially in nested formulas.

```powerfx
Filter(
    Requests As request,
    request.Status = "Open" && request.Requester.Email = User().Email
)
```

This is clearer than relying on implicit field resolution.

## Formula Size

Avoid overly long formulas. Split complex logic with:

- `With()` for local names.
- Named formulas for reusable computed values.
- Components for reusable UI behavior.
- Variables for state, not as a substitute for readable design.

## Delegation Warning

Filtering, searching, sorting, aggregating, and using `LookUp()` can trigger delegation issues. If the connector is unknown, say delegation cannot be confirmed. For SharePoint and Excel, be especially cautious. For Dataverse, more operations are delegable, but formula shape still matters.
