# PowerPlatformExpert

PowerPlatformExpert is an offline Mistral Vibe skill for Power Platform development guidance. It helps answer questions about Canvas Apps, Power Fx, SharePoint, Dataverse, Power Automate, delegation, responsive design, accessibility, troubleshooting, and ALM.

## What This Skill Is

- A practical offline expert pack.
- A curated best-practice guide for common Power Apps and Power Platform work.
- A reusable skill folder that can be uploaded directly to Mistral Vibe.
- A source-aware assistant guide that tells the model not to invent Power Fx functions, connector capabilities, or delegation support.

## What This Skill Is Not

- It is not a full copy of Microsoft Learn.
- It is not a live documentation connector.
- It does not inspect Power Apps Studio, Dataverse metadata, SharePoint lists, tenant settings, or user credentials.
- It cannot guarantee current product behavior unless the content is refreshed against Microsoft documentation.

## Current Coverage

The skill includes:

- `SKILL.md` as the main entry point.
- Power Fx formula guidance and examples.
- Canvas App design and maintainability guidance.
- Canvas component, component library, and code component guidance.
- Delegation guidance for SharePoint, Dataverse, Excel, and common formula patterns.
- Responsive layout guidance using containers.
- SharePoint and Dataverse data source guidance.
- Power Automate integration patterns.
- ALM guidance for solutions, environment variables, connection references, managed/unmanaged solutions, and pipelines.
- Accessibility and troubleshooting checklists.
- Example formulas and reusable review templates.

## Does It Have All Power Fx Information?

No. Power Fx is too large and changes over time. This skill includes practical guidance for common Canvas App formulas such as `With()`, `Patch()`, `SubmitForm()`, `LookUp()`, `Filter()`, `SortByColumns()`, `Search()`, `ForAll()`, `Concurrent()`, `Coalesce()`, `IfError()`, `IsBlank()`, `IsEmpty()`, record scope, `ThisRecord`, and `As`.

For exhaustive function behavior, syntax, and host-specific differences, refresh against the official Microsoft Learn Power Fx reference.

## Does It Have All Component Information?

No. It now covers the major component categories:

- Canvas components.
- Component libraries.
- Custom properties.
- Reusable component patterns.
- Power Apps component framework, also called code components or PCF.
- Key limitations and decision guidance.

It does not replace Microsoft Learn for full PCF API details, CLI commands, manifest schema, packaging, or current limitations.

## Recommended Microsoft Learn Sources

Review these official sources when refreshing the skill:

- Power Fx overview: https://learn.microsoft.com/en-us/power-platform/power-fx/overview
- Power Fx formula reference for canvas apps: https://learn.microsoft.com/en-us/power-platform/power-fx/formula-reference-canvas-apps
- Power Fx formula reference overview: https://learn.microsoft.com/en-us/power-platform/power-fx/formula-reference-overview
- Filter, Search, and LookUp: https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-filter-lookup
- Patch: https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-patch
- With function: https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-with
- Get started with formulas in canvas apps: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/working-with-formulas
- Canvas app data sources: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/working-with-data-sources
- Canvas apps documentation: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/
- Delegation overview: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-overview
- Canvas components: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-component
- Canvas component properties: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/component-properties
- Component libraries: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/component-library
- Power Apps component framework overview: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/overview
- Code components for canvas apps: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/component-framework-for-canvas-apps
- PCF limitations: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/limitations
- PCF best practices: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/code-components-best-practices
- ALM overview: https://learn.microsoft.com/en-us/power-platform/alm/overview-alm
- Power Platform pipelines: https://learn.microsoft.com/en-us/power-platform/alm/pipelines
- Environment variables: https://learn.microsoft.com/en-us/power-apps/maker/data-platform/environmentvariables
- Connection references: https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-connection-reference

## Refresh Process

Use this process periodically because Power Platform changes often:

1. Review the Microsoft Learn sources above.
2. Update only guidance that changed or needs more precision.
3. Keep the skill offline and self-contained.
4. Do not add executable scripts.
5. Keep `SKILL.md` concise and use `docs/`, `examples/`, and `templates/` for detail.
6. Preserve uncertainty rules: do not invent functions, connector capabilities, or delegation support.
7. Re-check that all referenced files exist and no file is empty.

## Improvement Ideas

High-value future improvements:

- Add connector-specific delegation tables for SharePoint, Dataverse, SQL, and Excel, with explicit uncertainty notes.
- Add a delegation wording test set that rejects answers claiming SharePoint indexing guarantees delegation, non-delegable formulas download all rows, or `IfError()` catches delegation warnings.
- Add deeper Dataverse security examples for business units, teams, ownership, and field security.
- Add more Power Automate patterns for approvals, retries, scopes, and child flows.
- Add a PCF/code component checklist for professional developer projects.
- Add more formula examples for forms, validation, attachments, date filters, and search.
- Add governance guidance for environments, DLP policies, licensing, and CoE practices.

## Last Reviewed

Last reviewed against Microsoft Learn on 2026-07-02.
