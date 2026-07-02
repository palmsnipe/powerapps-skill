# ALM and Solutions

Application lifecycle management keeps Power Platform apps reliable across development, test, and production.

## Environments

Use separate environments for development, test, and production when the app matters. Avoid building directly in production.

## Unmanaged in Development

Use unmanaged solutions in development. Makers can edit components and iterate.

Include related components:

- Canvas Apps.
- Flows.
- Dataverse tables and choices.
- Environment variables.
- Connection references.
- Security roles.
- Custom connectors where applicable.

## Managed in Test and Production

Export managed solutions to test and production. Managed solutions reduce accidental edits and support clearer upgrade behavior.

## Publishers

Use a consistent publisher and prefix. This helps identify ownership and avoids naming collisions.

## Versioning

Use meaningful solution versions. Increment versions intentionally for releases, and document important changes.

## Environment Variables

Use environment variables for values that differ by environment:

- URLs.
- IDs.
- Mailboxes.
- Feature flags.
- Integration settings.

Set values during deployment rather than editing app formulas per environment. Use them for configuration values, not as a substitute for connection references, security roles, or relational configuration tables.

## Connection References

Use connection references for solution-aware flows and apps where supported. This makes deployment cleaner and avoids hard-coded personal connections. Environment variables hold configuration values; connection references hold connector connection references.

## Deployment Pipelines

Use Power Platform pipelines or an equivalent controlled deployment process. Pipelines help promote solutions through environments with fewer manual steps.

## Solution Layering

Understand that multiple managed solutions can layer components. Unexpected behavior can come from layers, unmanaged customizations, or direct production edits.

When troubleshooting, check:

- Which solution owns the component.
- Whether unmanaged customizations exist.
- Whether an older managed layer remains.
- Whether dependencies are missing.

## Avoid Editing Directly in Production

Direct production edits create drift and make future deployments risky. Emergency fixes should be reconciled back into development and redeployed through the normal path.

## Document Dependencies

Document:

- Data sources.
- Environment variables.
- Connection references.
- Required security roles.
- Flows called by apps.
- Custom connectors.
- External systems.
- SharePoint lists or Dataverse tables.

## Practical Release Checklist

- Solution contains all required components.
- Environment variables have values in target environment.
- Connection references are configured.
- Flows are turned on and owned appropriately.
- Users have required security roles and licenses.
- App has been tested with non-maker user permissions.
- Rollback or hotfix plan exists.
