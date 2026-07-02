# Variables and State

Use variables for state. Prefer formulas for values that can be derived.

## Global Variable

Use `Set()` for app-wide state.

```powerfx
Set(varCurrentUserEmail, User().Email)
```

Good uses:

- Current user information used across screens.
- Theme or feature flags.
- App-wide selected context.

Avoid using global variables as hidden dependencies for every screen.

## Screen Context Variable

Use `UpdateContext()` for screen-local UI state.

```powerfx
UpdateContext({ locShowConfirmDelete: true })
```

Good uses:

- Dialog visibility.
- Submit attempt state.
- Local edit mode.
- Temporary screen-only flags.

## Collections

Use collections for small local tables or temporary selected records.

```powerfx
Collect(colSelectedRequests, ThisItem)
```

Risky:

```powerfx
ClearCollect(colAllRequests, Requests)
```

Do not collect a large remote table to bypass delegation. It may only collect the first configured page and can become stale.

## Named Formulas

Prefer named formulas for reusable computed values when available.

Good candidates:

- Current user email.
- App theme values.
- Derived configuration.
- Reusable calculations that do not require behavior actions.

Do not put named formulas in `App.OnStart`. Named formulas are calculated when needed; `OnStart` is for behavior actions.

## Loading and Saving State

```powerfx
UpdateContext({ locSaving: true });
IfError(
    Patch(Requests, galRequests.Selected, { Title: Trim(txtTitle.Text) }),
    Notify("Save failed.", NotificationType.Error),
    Notify("Saved.", NotificationType.Success)
);
UpdateContext({ locSaving: false })
```

Disable buttons while saving:

```powerfx
If(locSaving, DisplayMode.Disabled, DisplayMode.Edit)
```

## Review Checklist

- Is this value truly state, or can it be a formula?
- Is the scope global, screen-local, or component-local?
- Could this collection hide a delegation problem?
- Does save/loading state prevent duplicate actions?
