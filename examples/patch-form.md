# Patch and Form Examples

Use `Patch()` for custom save behavior. Use forms and `SubmitForm()` when built-in form behavior fits the screen.

## Patch New Record

```powerfx
IfError(
    Patch(
        Requests,
        Defaults(Requests),
        {
            Title: txtTitle.Text,
            Description: txtDescription.Text,
            DueDate: dpDueDate.SelectedDate
        }
    ),
    Notify("The request could not be created.", NotificationType.Error),
    Notify("Request created.", NotificationType.Success)
)
```

## Patch Existing Record

```powerfx
IfError(
    Patch(
        Requests,
        galRequests.Selected,
        {
            Title: txtTitle.Text,
            Description: txtDescription.Text,
            DueDate: dpDueDate.SelectedDate
        }
    ),
    Notify("The request could not be saved.", NotificationType.Error),
    Notify("Request saved.", NotificationType.Success)
)
```

## Patch with Choice Column

SharePoint choice column:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Title: txtTitle.Text,
        Status: { Value: ddStatus.Selected.Value }
    }
)
```

Dataverse choice syntax depends on the choice/table names and selected item shape. If the combo box is bound directly to compatible Dataverse choice values, a common pattern is:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Name: txtName.Text,
        Status: cmbStatus.Selected.Value
    }
)
```

Confirm the actual Dataverse choice type and selected item shape.

## Patch with Person Column in SharePoint

Person column shapes vary. A common pattern:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Title: txtTitle.Text,
        Requester: {
            Claims: "i:0#.f|membership|" & Lower(cmbRequester.Selected.Mail),
            DisplayName: cmbRequester.Selected.DisplayName,
            Email: cmbRequester.Selected.Mail
        }
    }
)
```

If the combo box uses a different user source, field names may be `Email`, `Mail`, `DisplayName`, or similar. Some older examples include an `@odata.type` field for SharePoint expanded user records. Ask for the combo box `Items` formula and selected record shape when unsure.

## Patch with Dataverse Lookup

If `cmbAccount.Items` is the Dataverse `Accounts` table and the lookup column expects an Account record:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Name: txtRequestName.Text,
        Account: cmbAccount.Selected
    }
)
```

If the selected item is not from the lookup target table, use `LookUp()` carefully:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Name: txtRequestName.Text,
        Account: LookUp(Accounts, Account = cmbAccount.Selected.Account)
    }
)
```

Delegation warning: confirm the `LookUp()` predicate is delegable for the Dataverse table and column.

## `IfError()` Around `Patch()`

```powerfx
IfError(
    Patch(
        Requests,
        galRequests.Selected,
        {
            Status: "Approved",
            ApprovedOn: Now()
        }
    ),
    Notify("Approval failed. Try again or contact support.", NotificationType.Error),
    Notify("Request approved.", NotificationType.Success)
)
```

## Notify Success and Failure

For forms:

```powerfx
// Submit button OnSelect
If(
    frmRequest.Valid,
    SubmitForm(frmRequest),
    Notify("Fix the highlighted fields before submitting.", NotificationType.Error)
)
```

```powerfx
// frmRequest.OnSuccess
Notify("Request saved.", NotificationType.Success);
Back()
```

```powerfx
// frmRequest.OnFailure
Notify("Request could not be saved: " & frmRequest.Error, NotificationType.Error)
```

## Practical Save Checklist

- Validate required fields before save.
- Disable the submit button while saving.
- Use `IfError()` or form `OnFailure`.
- Refresh or update local state after save.
- Confirm field shapes for choice, lookup, and person columns.
- Do not assume user permissions; test with a non-maker account.
