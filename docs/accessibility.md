# Accessibility

Accessible Canvas Apps are easier for everyone to use. Accessibility should be part of design, not a final pass.

## Labels and Accessible Names

Every meaningful input, icon button, and interactive control needs a clear accessible name.

Use visible labels where possible. For icon-only buttons, set accessible label properties so screen reader users know the action.

Good labels:

- `Submit request`
- `Open navigation menu`
- `Delete selected item`

Poor labels:

- `Button`
- `Icon`
- `Click here`

## Tab Order

Keyboard focus should move in the same logical order as the visual layout:

1. Header and primary navigation.
2. Main content.
3. Form fields.
4. Actions.

Avoid hidden controls receiving focus.

## Keyboard Navigation

Users should be able to:

- Reach interactive controls with the keyboard.
- Activate buttons and commands.
- Move through forms in order.
- Understand current focus.

Avoid interactions that require mouse-only hover behavior.

## Color Contrast

Use sufficient contrast for text, icons, borders, and state indicators. Do not rely on color alone to communicate status.

Good:

- Red error text plus an error icon and message.
- Status label with text, not only color.

Bad:

- Green and red dots without text.

## Focus Indicators

Keep focus indicators visible. Do not remove outlines or focus styling unless replacing them with an equally visible alternative.

## Screen Reader Friendly Text

Use clear labels, headings, and messages. Avoid ambiguous announcements.

For dynamic content:

- Show clear success and error messages.
- Keep messages near the affected control where possible.
- Avoid rapidly changing text that is difficult to follow.

## Error Messages

Error messages should:

- Identify the field or action.
- Explain what is wrong.
- Tell the user how to fix it.

Good:

```text
Due date is required. Select a date before submitting.
```

Bad:

```text
Invalid input.
```

## Accessibility Review Checklist

- Interactive controls have accessible names.
- Tab order is logical.
- Keyboard users can complete the workflow.
- Focus is visible.
- Text has adequate contrast.
- Required fields are identified.
- Errors are specific and recoverable.
- Status is not communicated by color alone.
