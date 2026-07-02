# Canvas App Screen Design Review

Use this checklist to review a Canvas App screen. Start by identifying the app type, data source, screen purpose, and target devices.

## Context

- Screen name:
- Primary user task:
- Data source:
- Target devices:
- User roles:
- Known constraints:

## Layout

- Uses containers for primary layout.
- Avoids unnecessary hard-coded `X` and `Y` positions.
- Works at desktop, tablet, and mobile widths.
- Important content does not overlap or clip.
- Actions are placed near the content they affect.
- Header, content, and footer/action areas are clear.

## Controls

- Controls have meaningful names.
- Repeated UI is componentized where useful.
- Forms are used where built-in validation is helpful.
- Custom controls are justified when forms do not fit.
- Modern controls are used consistently and tested.

## Data and Delegation

- Gallery `Items` formulas are reviewed for delegation.
- Filters use simple, source-aware predicates.
- Search behavior is appropriate for the connector.
- Sorts use real columns where possible.
- Large data sources are not blindly collected.
- SharePoint formulas separate delegation support from indexing/list-threshold concerns.
- SharePoint filter and sort columns are indexed where needed for scale.

## State and Behavior

- Variables are limited to useful state.
- Navigation passes context clearly.
- Loading states exist for long-running actions.
- Save buttons prevent duplicate submissions.
- Empty states are present.
- Error states are present.

## Validation and Errors

- Required fields are clearly marked.
- Inline validation appears near fields.
- Save failures show useful messages.
- Technical details are not exposed unnecessarily.
- Form `OnSuccess` and `OnFailure` or `IfError()` are used.

## Security

- The screen does not expose actions users should not have.
- App logic is not the only security boundary.
- Data source permissions are considered.
- Sensitive values are not hard-coded in formulas.

## Accessibility

- Interactive controls have accessible names.
- Tab order is logical.
- Keyboard navigation works.
- Focus indicators are visible.
- Color contrast is sufficient.
- Status is not communicated by color alone.
- Error messages explain how to fix the issue.

## Review Output

Summarize:

- Highest-risk issues.
- Recommended fixes.
- Delegation/performance concerns.
- Accessibility concerns.
- Questions that need app or data source details.
