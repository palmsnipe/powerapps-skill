# Power Fx Formula Review

Use this checklist to review a Power Fx formula. Prioritize correctness, delegation, readability, and maintainability.

## Context

- Formula:
- Property:
- Control:
- Screen:
- Data source:
- Connector:
- Column types involved:
- Expected behavior:

## Correctness

- Formula uses valid Power Fx functions.
- Referenced controls and fields are plausible.
- Field types match assigned values.
- Choice, person, and lookup fields use the correct shape.
- Blank and empty checks use `IsBlank()` or `IsEmpty()` appropriately.
- Error paths are handled.

## Delegation

- Connector is identified.
- `Filter()`, `LookUp()`, sort, search, and aggregation are reviewed.
- Formula avoids wrapping data source columns in functions where delegation matters.
- Formula avoids risky `in` or broad `Search()` patterns for large SharePoint/Excel data.
- SharePoint filters use indexed columns where appropriate.
- Uncertain delegation is called out explicitly.

## Readability

- Formula is short enough to understand.
- Repeated expressions are named with `With()`.
- Record scope is clear with `As` where useful.
- Control names are meaningful.
- Nested `If()` or repeated logic is simplified where possible.

## State

- Variables are necessary and scoped appropriately.
- `Set()` is not used when a computed value would be better.
- `UpdateContext()` is used for screen-local state.
- Collections are not used to hide delegation problems.

## Error Handling

- `Patch()` and flow calls use `IfError()` or equivalent handling.
- Form saves use `OnSuccess` and `OnFailure`.
- User messages are clear and recoverable.

## Suggested Response Format

1. State whether the formula is likely correct.
2. Identify delegation, type, or maintainability risks.
3. Provide a revised formula.
4. Explain important changes.
5. Ask for missing connector or schema details if needed.
