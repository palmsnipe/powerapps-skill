# Troubleshooting

Troubleshoot Power Platform issues by identifying the app type, data source, connector, formula, control, and environment. Do not assume tenant configuration or live metadata.

## Delegation Warnings

Symptoms:

- Blue underline or delegation warning in Studio.
- Missing records in galleries.
- Search works in small data but fails at scale.
- Known matching records beyond the first 500/2,000 do not appear.

Check:

- Connector type.
- Column type.
- Formula operators.
- Data row limit setting.
- Whether filter columns are indexed.
- Whether the answer is mixing up delegation support with SharePoint indexing.

Safer approach:

- Use simple predicates.
- Use a delegation-friendly formula shape.
- Index SharePoint filter/sort columns for large-list performance.
- Avoid `in`, calculated expressions, and functions around data source columns.
- Prefer Dataverse for relational or large-scale apps.

Do not tell users that indexing fixes delegation. Indexes help SharePoint evaluate large-list queries; they do not make `Lower()`, `Year()`, `in`, or unsupported operations delegable.

Do not say the app downloads all rows and filters locally. For delegable data sources with non-delegable formulas, Power Apps processes only the first configured row-limit records locally, commonly 500 by default and up to 2,000.

Do not suggest `IfError()` as a delegation-warning fix. Use `IfError()` for runtime failures, not authoring-time delegation warnings.

## Invalid Argument Types

Symptoms:

- Formula errors after changing a control or data source.
- Patch fails because a field expects a record, not text.

Check:

- Column type.
- Control selected value shape.
- Choice, person, lookup, and Dataverse lookup fields.

Example issue:

```powerfx
Patch(Requests, Defaults(Requests), { Status: "Open" })
```

For a SharePoint choice column, this may need:

```powerfx
Patch(Requests, Defaults(Requests), { Status: { Value: "Open" } })
```

## Patch Failures

Check:

- Required fields.
- Field types.
- User permissions.
- Data source validation.
- Network or connector errors.
- Whether the target record exists.

Use:

```powerfx
IfError(
    Patch(Requests, galRequests.Selected, { Title: txtTitle.Text }),
    Notify("Save failed.", NotificationType.Error),
    Notify("Saved.", NotificationType.Success)
)
```

For deeper diagnostics, inspect `Errors(DataSource)` where appropriate.

## Form Not Submitting

Check:

- Form mode: New, Edit, or View.
- `DataSource` and `Item` properties.
- Required cards.
- Card `Update` properties.
- Form `Valid` property.
- `OnSuccess` and `OnFailure`.

Common pattern:

```powerfx
If(
    frmRequest.Valid,
    SubmitForm(frmRequest),
    Notify("Fix the highlighted fields before submitting.", NotificationType.Error)
)
```

## Gallery Not Refreshing

Check:

- Gallery `Items` formula.
- Whether the data source was refreshed.
- Whether local collections are stale.
- Whether filters exclude the new or updated record.
- Whether sorting hides the record.

After a save:

```powerfx
Refresh(Requests);
Reset(galRequests)
```

If using collections, update the collection deliberately instead of assuming it refreshes.

## Data Source Schema Changes

Symptoms:

- Missing fields.
- Invalid names.
- Cards stop working.
- Patch formulas fail.

Check:

- Refresh the data source in the app.
- Re-add affected fields to forms if needed.
- Review renamed or deleted columns.
- Update formulas referencing old names.

## Power Automate Connection Errors

Check:

- Flow is turned on.
- Flow owner and connections are valid.
- User has permission to run the flow.
- Connection references are configured in the target environment.
- Trigger inputs still match the app call.

In the app, wrap flow calls in `IfError()` and show a clear failure message.

## Environment Variable Issues

Symptoms:

- Flow points to the wrong site or mailbox.
- App behaves differently after deployment.
- Missing configuration in test or production.

Check:

- Current value exists in the target environment.
- Variable was included in the solution.
- Flow or app actually references the environment variable.
- Deployment pipeline prompts were completed.

## App Performance Problems

Check:

- Large data loads on startup.
- Non-delegable galleries.
- Expensive formulas inside gallery templates.
- Repeated connector calls.
- Too many controls on one screen.
- Large images or media.
- Unnecessary flows.

Improve by:

- Filtering server-side.
- Loading only required data.
- Using named formulas for computed values.
- Simplifying gallery templates.
- Using components carefully.
- Avoiding unnecessary collections.
