# Component Patterns

Use components for repeated UI and behavior. Keep them generic and configurable through input and output properties.

## Reusable Header Component

Purpose:

- App title.
- Current screen title.
- Optional back button.
- Optional primary action.

Suggested input properties:

- `TitleText`
- `SubtitleText`
- `ShowBack`
- `ShowPrimaryAction`
- `PrimaryActionText`

Suggested output/event properties:

- `OnBack`
- `OnPrimaryAction`

Usage:

```powerfx
cmpHeader.TitleText = "Requests"
cmpHeader.ShowBack = false
cmpHeader.PrimaryActionText = "New request"
```

Keep the header component free of direct screen-specific data source calls.

## Navigation Component

Purpose:

- Consistent navigation across screens.
- Selected item state.
- Optional role-based visibility.

Suggested input properties:

- `Items`
- `SelectedKey`
- `IsCompact`

Example `Items` table:

```powerfx
Table(
    { Key: "home", Label: "Home", Target: scrHome },
    { Key: "requests", Label: "Requests", Target: scrRequests },
    { Key: "admin", Label: "Admin", Target: scrAdmin }
)
```

Navigation button formula inside the component can use the selected item target if the app design supports passing screens this way. If not, expose selected key and let the host screen decide.

## Confirmation Dialog Component

Purpose:

- Confirm destructive or important actions.

Suggested input properties:

- `Visible`
- `TitleText`
- `MessageText`
- `ConfirmText`
- `CancelText`

Suggested event properties:

- `OnConfirm`
- `OnCancel`

Host screen:

```powerfx
UpdateContext({ locConfirmDeleteVisible: true })
```

Confirm action:

```powerfx
IfError(
    Remove(Requests, galRequests.Selected),
    Notify("Delete failed.", NotificationType.Error),
    Notify("Deleted.", NotificationType.Success)
);
UpdateContext({ locConfirmDeleteVisible: false })
```

## Loading Overlay Component

Purpose:

- Block duplicate actions.
- Communicate that work is in progress.

Suggested input properties:

- `Visible`
- `MessageText`

Host screen:

```powerfx
UpdateContext({ locSaving: true });
IfError(
    Patch(Requests, galRequests.Selected, { Status: "Submitted" }),
    Notify("Save failed.", NotificationType.Error),
    Notify("Submitted.", NotificationType.Success)
);
UpdateContext({ locSaving: false })
```

Also set submit button `DisplayMode`:

```powerfx
If(locSaving, DisplayMode.Disabled, DisplayMode.Edit)
```

## Input Validation Component

Purpose:

- Reusable required field or validation message.

Suggested input properties:

- `IsValid`
- `MessageText`
- `ShowWhenValid`

Example:

```powerfx
cmpTitleValidation.IsValid = !IsBlank(txtTitle.Text)
cmpTitleValidation.MessageText = "Title is required."
```

Use inline validation near fields. Do not rely only on `Notify()` for validation errors.

## Component Guidelines

- Do not hard-code data source names inside reusable components unless the component is intentionally data-specific.
- Use input properties for labels, visibility, and behavior.
- Use output/event properties for actions.
- Keep component formulas readable.
- Test components in narrow and wide layouts.
- Ensure component controls have accessible names.
