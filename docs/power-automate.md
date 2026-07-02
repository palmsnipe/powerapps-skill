# Power Automate with Power Apps

Use Power Automate when work should run outside the app, integrate with services, perform approval or notification workflows, or handle logic that would be awkward or insecure in Canvas Apps.

## When to Use a Flow

Use a flow for:

- Sending approval requests.
- Sending emails with attachments.
- Calling connectors not available or practical in the app.
- Long-running background work.
- Cross-system orchestration.
- Server-side operations that should not depend on the user's open app session.

Avoid flows for simple CRUD that Power Apps can do directly against the data source.

## Power Apps Trigger

A flow called from Power Apps usually starts with a Power Apps trigger, commonly the Power Apps or Power Apps (V2) trigger depending on the flow design and environment. Keep inputs explicit and minimal:

- Record ID.
- Action type.
- Comment text.
- User email when needed.

Do not pass entire large records unless required.

## Passing Parameters

Power Fx call:

```powerfx
Set(
    varFlowResult,
    SubmitApprovalFlow.Run(
        Text(galRequests.Selected.ID),
        txtApprovalComment.Text
    )
)
```

Validate inputs before calling the flow.

## Returning Values

Return a simple result object or values that the app can handle:

- `success`
- `message`
- `recordId`
- `errorCode`

Power Fx pattern:

```powerfx
IfError(
    Set(varFlowResult, SubmitApprovalFlow.Run(Text(galRequests.Selected.ID))),
    Notify("The flow could not be started.", NotificationType.Error),
    If(
        varFlowResult.success,
        Notify(varFlowResult.message, NotificationType.Success),
        Notify(varFlowResult.message, NotificationType.Error)
    )
)
```

Actual return property names depend on how the flow response is configured.

## Error Handling

In the flow:

- Use scopes for Try/Catch-style structure.
- Return meaningful failure information.
- Log technical details somewhere appropriate.
- Avoid exposing sensitive details to end users.

In the app:

- Use `IfError()`.
- Disable buttons while the flow runs.
- Show user-friendly messages.

## Connection Ownership

Flows run under configured connections. Understand who owns those connections and what permissions they grant. Avoid tying production-critical flows to an individual maker's personal account when a service account or managed ownership model is required.

## Service Accounts

Use service accounts or dedicated owners where organizational policy supports them. Keep permissions least-privilege and document ownership.

## Environment Variables

Use environment variables for environment-specific values such as email addresses, site URLs, list names, IDs, and feature settings. Use connection references for connector connections. This reduces app and flow edits during deployment.

## Child Flows

Use child flows for reusable automation inside solution-aware flows. Keep child flow contracts clear: inputs, outputs, error behavior, and ownership.

## Avoiding Unnecessary Flows

Do not use a flow when:

- A simple `Patch()` is enough.
- The app can filter or lookup data directly.
- The flow only mirrors a formula.
- The flow adds latency and another failure point without improving security, reliability, or maintainability.
