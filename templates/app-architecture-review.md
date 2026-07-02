# Power Apps Solution Architecture Review

Use this checklist to review a full Power Apps solution. Start by identifying business purpose, app type, data sources, environments, and integration points.

## Context

- App name:
- Business process:
- App type:
- Primary data source:
- Secondary data sources:
- User roles:
- Environments:
- Integrations:
- Current pain points:

## Data Model

- Data source matches the app's complexity.
- Dataverse is considered for relational, secure, or business-critical apps.
- SharePoint is limited to appropriate list-based scenarios.
- Excel is not used as a production backend for large or critical apps.
- Relationships are modeled explicitly.
- Choice/status values are controlled.
- Required fields and validation rules are defined.

## Canvas App Design

- Screens map to user tasks.
- Responsive containers are used.
- Components are used for repeated UI.
- Control names are meaningful.
- Form and custom `Patch()` patterns are chosen intentionally.
- Loading, empty, and error states are present.
- App startup is not overloaded.

## Power Fx

- Formulas are readable and maintainable.
- `With()` and named formulas are used where helpful.
- Variables are used only for real state.
- Collections are not used as a workaround for large data.
- Save logic handles errors.
- Record scope is clear.

## Delegation and Performance

- Gallery filters are delegable or risks are documented.
- Search patterns are connector-aware.
- Sorts and lookups are reviewed.
- Large data sets use indexed/searchable columns.
- Expensive formulas are avoided inside gallery templates.
- Startup loads only necessary data.

## Power Automate

- Flows are used only where they add value.
- Flow inputs and outputs are explicit.
- Errors are returned clearly.
- Connection ownership is appropriate.
- Service account or managed ownership approach is documented when needed.
- Child flows are used for reusable solution-aware automation where useful.

## Security

- Data source permissions enforce real access control.
- App visibility rules do not replace security.
- Dataverse security roles or SharePoint permissions are documented.
- Service accounts use least privilege.
- Sensitive configuration is not hard-coded.

## ALM

- Components are in solutions.
- Development uses unmanaged solutions.
- Test and production use managed solutions.
- Environment variables are used for environment-specific values.
- Connection references are used where applicable.
- Deployment pipelines or controlled promotion are used.
- Dependencies are documented.
- Direct production edits are avoided.

## Accessibility

- Keyboard navigation works.
- Focus order is logical.
- Accessible names are present.
- Contrast is sufficient.
- Errors are specific.
- Status is not color-only.

## Review Output

Provide:

- Executive summary.
- Top risks.
- Recommended target architecture.
- Short-term fixes.
- Medium-term improvements.
- Open questions.
