# SharePoint as a Power Apps Data Source

SharePoint lists are useful for simple business apps, lightweight approvals, and team-owned data. They are not a replacement for a relational application database.

## List Column Types

Common column types:

- Single line of text.
- Multiple lines of text.
- Number.
- Date and time.
- Choice.
- Yes/no.
- Person or group.
- Lookup.
- Attachments.

Power Fx formulas differ by column type. For example, a SharePoint choice column often uses `.Value`, and a person column stores a record-like value with claims and profile fields.

## Choice Columns

Filtering:

```powerfx
Filter(Requests, Status.Value = "Open")
```

Patching:

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

## Person Columns

Person fields often require a specific record shape when patching. A common SharePoint person field pattern includes claims, display name, and email.

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

Actual field names can vary based on the people picker source and connector behavior. Some older examples include an `@odata.type` field for SharePoint expanded user records; do not add it blindly unless the app/schema requires it.

## Lookup Columns

Lookup columns usually require an `Id` and `Value` shape:

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

Confirm the exact expected shape in the app because SharePoint lookup configuration can vary.

## Delegation Risks

SharePoint delegation needs care:

- Prefer simple equality filters and source-supported operations.
- Index filter and sort columns for large lists, but do not treat indexing as proof of delegation.
- Use `StartsWith()` where supported for prefix matching.
- Do not say `StartsWith()` delegates only if the column is indexed. Verify `StartsWith()` delegation based on connector support, column type, and formula shape; use indexes separately for large-list performance.
- Avoid `in` for large lists.
- Avoid calculated columns in filters for large lists.
- Avoid wrapping data source columns in functions inside predicates.
- For person columns, prefer `Email` over `DisplayName` for identity stability, but still verify delegation.
- For choice and lookup columns, check the subfield and operation. Do not assume every operation on `.Value` is delegable.
- Do not suggest calculated columns such as `TitleLower = LOWER([Title])` as a reliable delegation workaround for case-insensitive search. Use a real maintained text column or a more appropriate data/search service.

Risky:

```powerfx
Filter(Requests, Lower(Title) = Lower(txtSearch.Text))
```

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

This is safer because `Title` is normally a text column. It is not a guarantee for every SharePoint schema. Verify in Power Apps Studio and current Microsoft Learn documentation. Index `Title` separately if needed for large-list performance; indexing is not what makes `StartsWith()` delegable.

## Attachments

SharePoint attachments are easiest through Edit forms because the attachment control is built for SharePoint integration. Custom attachment flows are more complex and often better handled with a form or Power Automate when requirements go beyond standard behavior.

## Patching Records

Patch simple fields directly:

```powerfx
Patch(
    Requests,
    galRequests.Selected,
    {
        Title: txtTitle.Text,
        DueDate: dpDueDate.SelectedDate,
        IsUrgent: chkUrgent.Value
    }
)
```

Wrap in `IfError()` for user feedback.

## Filtering Large Lists

For large lists:

- Index filter columns in SharePoint.
- Use simple predicates.
- Filter before sorting.
- Avoid collecting the entire list.
- Consider Dataverse if relationships, security, or scale are important.
- If users need case-insensitive contains search, do not rely on SharePoint calculated columns. Standard SharePoint list filtering generally does not provide delegable contains search. Consider Dataverse search, Microsoft Search/custom API, or a real normalized text column populated by app/flow/process logic and then verify delegation.

Important distinction:

- Delegation support comes from the connector and formula shape.
- SharePoint indexes help SharePoint execute large-list queries efficiently.
- Indexing does not make `Lower(Title)`, `txtSearch.Text in Title`, `Year(DueDate)`, or other non-delegable formula shapes delegable.

## When SharePoint Is Suitable

SharePoint is suitable when:

- Data is list-shaped and simple.
- Security can be handled at list or item level.
- Data volume is moderate.
- Relationships are light.
- Users already work in SharePoint.

## When Dataverse Is Better

Prefer Dataverse when:

- The app is business-critical.
- Data is relational.
- Security roles matter.
- ALM is important.
- You need stronger data modeling, auditing, or performance.
- The app will grow beyond a simple list.
