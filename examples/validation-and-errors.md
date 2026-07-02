# Validation and Error Patterns

Use validation and runtime error handling together. They solve different problems.

## Required Field Validation

```powerfx
With(
    {
        titleText: Trim(txtTitle.Text),
        hasDueDate: !IsBlank(dpDueDate.SelectedDate)
    },
    If(
        IsBlank(titleText),
        Notify("Title is required.", NotificationType.Error),
        !hasDueDate,
        Notify("Due date is required.", NotificationType.Error),
        SubmitForm(frmRequest)
    )
)
```

## Inline Error Visibility

Label `Visible`:

```powerfx
IsBlank(Trim(txtTitle.Text)) && locSubmitAttempted
```

Label `Text`:

```powerfx
"Title is required."
```

Submit button:

```powerfx
UpdateContext({ locSubmitAttempted: true });
If(
    frmRequest.Valid,
    SubmitForm(frmRequest),
    Notify("Fix the highlighted fields before submitting.", NotificationType.Error)
)
```

## Patch Error Handling

```powerfx
IfError(
    Patch(
        Requests,
        galRequests.Selected,
        { Title: Trim(txtTitle.Text) }
    ),
    Notify("Save failed.", NotificationType.Error),
    Notify("Saved.", NotificationType.Success)
)
```

## Flow Error Handling

```powerfx
IfError(
    Set(
        varFlowResult,
        SubmitApprovalFlow.Run(Text(galRequests.Selected.ID), txtComment.Text)
    ),
    Notify("The flow could not be started.", NotificationType.Error),
    If(
        varFlowResult.success,
        Notify(varFlowResult.message, NotificationType.Success),
        Notify(varFlowResult.message, NotificationType.Error)
    )
)
```

Actual flow return property names depend on the response configured in Power Automate.

## Do Not Use `IfError()` For Delegation Warnings

Delegation warnings are authoring-time warnings. `IfError()` is for runtime errors from formulas, saves, connector calls, or flow calls.
