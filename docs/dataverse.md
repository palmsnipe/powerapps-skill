# Dataverse Basics

Dataverse is the preferred Power Platform data service for many business applications because it supports structured tables, relationships, security roles, solution packaging, and stronger ALM.

## Tables and Columns

Tables store records. Columns define typed fields such as text, number, date, currency, choice, lookup, and owner.

Use clear table and column names. Model business concepts directly rather than forcing relational data into flat text fields.

## Relationships

Dataverse supports relationships such as:

- One-to-many.
- Many-to-one.
- Many-to-many.

Use relationships instead of duplicating values across tables. For example, store a lookup to Account rather than copying account name into every request record.

## Choices

Choices are controlled values. Use them when the allowed options are known and managed. Avoid free-text status fields when the app logic depends on exact values.

Filtering example:

```powerfx
Filter(Requests, Status = 'Status (Requests)'.Open)
```

Choice syntax depends on the table, choice names, and whether the column uses a local or global choice. If a combo box is bound directly to the Dataverse choice values, compare the column to the selected choice value shape used by that control.

## Lookups

Dataverse lookups reference related records.

Patch example:

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

Confirm the lookup column name and selected record source.

## Security Roles

Dataverse security roles control table and row privileges. App logic should not be the only security boundary.

Consider:

- Table permissions.
- Business unit access.
- Owner/team ownership.
- Field security where needed.
- Least privilege for users and service accounts.

## Business Rules

Business rules can enforce simple validation and field behavior. Use them for rules that should apply consistently across model-driven forms and Dataverse behavior. For complex validation, consider plugins, Power Automate, or app-level checks depending on the requirement.

## Environment Variables

Use environment variables for values that change by environment:

- SharePoint site URLs.
- Flow endpoint references where applicable.
- Feature flags.
- Email addresses.
- Configuration IDs.

Store environment-specific values outside formulas where possible.

## Solution-Aware Apps

Put production apps, flows, tables, environment variables, and connection references in solutions. This enables managed deployment and dependency tracking.

## Advantages Over SharePoint

Dataverse is often better for:

- Relational data.
- Role-based security.
- ALM.
- Auditing.
- Business-critical apps.
- Larger data volumes.
- Complex validation and automation.

## Delegation and Performance

Dataverse generally has stronger delegation support than SharePoint and Excel, but delegation still depends on formula shape and column type.

Prefer:

```powerfx
Filter(Requests, Status = 'Status (Requests)'.Open)
```

Be cautious with:

```powerfx
Filter(Requests, Lower(Name) = Lower(txtSearch.Text))
```

Use server-side views, indexed/searchable columns, and simple predicates for large data sets.
