# Dataverse Field Shapes

Dataverse field syntax depends on table names, column names, local/global choices, and control item shapes. Do not invent schema.

## Choice Column

Filtering with a known choice value often looks like:

```powerfx
Filter(Requests, Status = 'Status (Requests)'.Open)
```

If a combo box is bound directly to compatible choice values:

```powerfx
Filter(Requests, IsBlank(cmbStatus.Selected.Value) || Status = cmbStatus.Selected.Value)
```

Verify the choice type and selected item shape.

## Lookup Column

If the combo box `Items` is the lookup target table:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Name: txtName.Text,
        Account: cmbAccount.Selected
    }
)
```

If selected items are not records from the lookup target table, use a careful `LookUp()`:

```powerfx
Patch(
    Requests,
    Defaults(Requests),
    {
        Name: txtName.Text,
        Account: LookUp(Accounts, Account = cmbAccount.Selected.Account)
    }
)
```

Verify the `LookUp()` predicate and delegation for the target table.

## Owner and Polymorphic Lookups

Dataverse has lookup columns that may point to different table types, such as owner-style fields. Do not assume simple `.Field` access is valid for every lookup. Ask for the table, column, and selected record shape.

## Environment-Aware Values

Use environment variables for configuration values that differ by environment. Use connection references for connector connections. Do not hard-code environment-specific IDs unless there is a clear reason.

## Review Checklist

- Confirm table and column logical/display names.
- Confirm local vs global choice.
- Confirm lookup target table.
- Verify security roles and table permissions.
- Include components in solutions for ALM.
