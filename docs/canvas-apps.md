# Canvas App Design and Maintainability

Canvas Apps are easiest to maintain when screens, controls, formulas, and data flows are intentionally organized.

## Screen Organization

Use screens that represent tasks, not database tables. Common patterns:

- Browse screen for search and list selection.
- Detail screen for read-only record review.
- Edit screen for creating or updating a record.
- Admin or settings screen for privileged configuration.

Avoid putting unrelated workflows on one screen. If a screen has many hidden panels, consider splitting the workflow or using components.

## Control Naming

Use names that show purpose and type:

- `galRequests`
- `frmRequest`
- `txtRequestTitle`
- `cmbDepartment`
- `btnSubmit`
- `conMain`

Avoid names such as `Button12` or `Gallery3`. Clear names make formulas easier to review.

## Modern Controls

Use modern controls when they fit the requirement and are supported in the target environment. Validate behavior for accessibility, theming, and feature gaps before standardizing on them. If the app already uses classic controls consistently, switch deliberately rather than mixing styles without a reason.

## Components

Use components for repeated UI and behavior:

- Header or app bar.
- Navigation.
- Confirmation dialog.
- Loading overlay.
- Reusable validation block.

Keep components generic. Pass data and state through input and output properties rather than referencing screen controls directly.

## Forms vs Custom Forms

Use Edit forms when:

- The screen maps closely to one record.
- Built-in validation and data cards are useful.
- The data source schema changes reasonably often.

Use custom forms with `Patch()` when:

- The UI does not map cleanly to one record.
- Multiple data sources are saved together.
- You need custom layout or conditional save behavior.

Do not use custom forms only to avoid learning form behavior. Forms provide useful validation, error handling, and metadata support.

## Galleries

Use galleries for lists and repeated layouts. Keep gallery templates simple:

- Avoid expensive formulas inside many repeated controls.
- Avoid per-row flow calls.
- Use explicit names with `As` in formulas.
- Show empty states when no records match.

Example empty state:

```powerfx
IsEmpty(galRequests.AllItems)
```

## Variables

Use variables intentionally:

- Global variables for app-wide state.
- Context variables for screen-local state.
- Collections for temporary local tables.

Avoid using variables for every value. Many values can be computed directly through formulas or named formulas.

## App `OnStart` vs Named Formulas

Use App `OnStart` for initialization that must run as a behavior action, such as loading a small configuration collection. Prefer named formulas for computed values that can update automatically.

Avoid placing too much app logic in `OnStart`, because it can slow startup and make behavior harder to reason about.

## Navigation

Use clear navigation paths:

```powerfx
Navigate(scrRequestDetail, ScreenTransition.Fade, { locRequest: galRequests.Selected })
```

Pass necessary context explicitly. Avoid relying on hidden global variables for every screen transition.

## Validation

Validate before saving:

```powerfx
If(
    IsBlank(txtTitle.Text),
    Notify("Title is required.", NotificationType.Error),
    SubmitForm(frmRequest)
)
```

Use visible inline messages for important field errors, not only temporary notifications.

## Error Handling

Use `IfError()` around custom saves and flow calls. Use form `OnFailure` for form saves. Show messages that help the user recover without exposing sensitive technical detail.

## Loading States

Use loading states for saves and long-running actions:

```powerfx
UpdateContext({ locSaving: true });
IfError(
    Patch(Requests, galRequests.Selected, { Status: "Submitted" }),
    Notify("Save failed.", NotificationType.Error),
    Notify("Saved.", NotificationType.Success)
);
UpdateContext({ locSaving: false })
```

Disable submit buttons while saving to prevent duplicate writes.

## Empty States

Every gallery or result area should handle no records:

- Say no records were found.
- Suggest clearing filters or creating a record if appropriate.
- Do not leave a blank area that looks broken.

## Maintainability Checklist

- Screens map to clear user tasks.
- Controls have meaningful names.
- Repeated UI is componentized.
- Save logic is centralized and handles errors.
- Delegation warnings are understood, not ignored.
- Loading, error, and empty states are present.
