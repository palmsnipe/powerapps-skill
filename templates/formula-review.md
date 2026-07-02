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
- SharePoint filters use a delegation-friendly formula shape where possible.
- SharePoint filter/sort columns are indexed where appropriate for large-list performance.
- The review does not claim indexing makes a non-delegable formula delegable.
- The review does not say non-delegable formulas load all rows client-side; it says only the first configured row-limit records are processed locally.
- The review avoids "Delegation-Safe" headings unless delegation is certain.
- The review does not suggest `IfError()` catches delegation warnings.
- The review does not suggest SharePoint calculated columns as delegation/search workarounds.
- Person `DisplayName` is treated as fragile for identity matching, not automatically declared non-delegable unless verified.
- Uncertain delegation is called out explicitly.

## Readability

- Formula is short enough to understand.
- Repeated expressions are named with `With()`.
- Optional search inputs are normalized once with `Trim()` and preserve an explicit blank-search guard.
- Full rewrites include the normalized `searchText` variable in `With()` and use it consistently.
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
5. Clearly separate delegation support from SharePoint indexing/list-threshold considerations.
6. Explain how to test with known matching records beyond the 500/2,000 row limit, not only visible gallery counts.
7. Ask for missing connector or schema details if needed.
