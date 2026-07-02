# SharePoint Field Shapes

SharePoint field shapes vary by column type and control source. Verify the actual selected record shape in the app.

## Choice Column

Filter:

```powerfx
Filter(Requests, Status.Value = "Open")
```

Patch:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Title: txtTitle.Text,
        Status: { Value: "Open" }
    }
)
```

## Person Column

Common person patch pattern:

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

Notes:

- Combo box user records might use `Mail`, `Email`, `DisplayName`, or other field names depending on `Items`.
- Some older examples include `@odata.type`; do not add it blindly.
- Prefer email for identity matching, but verify person-field delegation for the exact connector behavior.

## Lookup Column

Common lookup patch pattern:

```powerfx
Patch(
    Requests,
    galRequests.Selected,
    {
        Department: {
            Id: cmbDepartment.Selected.ID,
            Value: cmbDepartment.Selected.Title
        }
    }
)
```

## Attachments

Use Edit forms for standard SharePoint attachments when possible. The attachment control is designed for this scenario. Custom attachment handling is more complex and may require Power Automate.

## Review Checklist

- Confirm column type in SharePoint.
- Confirm combo box `Items` and selected record shape.
- Verify required fields before save.
- Wrap custom `Patch()` saves in `IfError()`.
- Verify delegation separately from SharePoint indexing.
