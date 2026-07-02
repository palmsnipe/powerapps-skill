# Power Automate Patterns

Power Apps should call flows when automation adds value. Do not use a flow for work that Power Apps can do more simply and reliably.

## Power Apps Calling a Flow

Button `OnSelect`:

```powerfx
If(
    IsBlank(galRequests.Selected.ID),
    Notify("Select a request first.", NotificationType.Warning),
    IfError(
        Set(
            varApprovalResult,
            StartApprovalFlow.Run(
                Text(galRequests.Selected.ID),
                txtComment.Text
            )
        ),
        Notify("The approval flow could not be started.", NotificationType.Error),
        Notify("Approval flow started.", NotificationType.Success)
    )
)
```

Keep flow parameters explicit and small.

## Flow Returning Success or Error

Design the flow response to return simple values:

```text
success: true/false
message: user-facing message
trackingId: optional ID
```

App pattern:

```powerfx
IfError(
    Set(
        varFlowResult,
        StartApprovalFlow.Run(Text(galRequests.Selected.ID), txtComment.Text)
    ),
    Notify("The flow call failed.", NotificationType.Error),
    If(
        varFlowResult.success,
        Notify(varFlowResult.message, NotificationType.Success),
        Notify(varFlowResult.message, NotificationType.Error)
    )
)
```

Actual property names depend on the flow response action.

## Flow for Complex Approval

Use a flow when approval requires:

- Approval connector actions.
- Escalation.
- Reminders.
- Multiple approvers.
- Audit trail updates.
- Email or Teams notifications.

Power Apps should start the process, show status, and let the flow manage long-running work.

## Flow for Email

Use a flow when:

- Email must come from a shared mailbox or service account.
- Attachments are involved.
- Message formatting is complex.
- Email should be logged or retried.

Do not hard-code personal mailbox assumptions. Connection ownership matters.

## When Not to Use a Flow

Avoid a flow when:

- A direct `Patch()` is enough.
- The app only needs to update the current record.
- The flow exists only to bypass simple formula work.
- The flow adds avoidable latency.
- Error handling would become harder.

Direct save example:

```powerfx
IfError(
    Patch(Requests, galRequests.Selected, { Status: "Submitted" }),
    Notify("Submit failed.", NotificationType.Error),
    Notify("Submitted.", NotificationType.Success)
)
```

## Practical Flow Checklist

- Inputs are explicit.
- Outputs are simple.
- Errors are handled in both flow and app.
- Connection owner is appropriate.
- Connection references are used in solutions.
- Environment-specific configuration values use environment variables.
- The user experience handles wait time and failure.
