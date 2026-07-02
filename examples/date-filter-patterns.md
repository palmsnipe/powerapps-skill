# Date Filter Patterns

Use date boundaries instead of applying date functions to data-source columns.

## Current Year

Bad:

```powerfx
Filter(Requests, Year(DueDate) = Year(Today()))
```

Better:

```powerfx
With(
    {
        startDate: Date(Year(Today()), 1, 1),
        endDate: Date(Year(Today()) + 1, 1, 1)
    },
    Filter(
        Requests,
        DueDate >= startDate &&
        DueDate < endDate
    )
)
```

## Current Month

```powerfx
With(
    {
        startDate: Date(Year(Today()), Month(Today()), 1),
        endDate: DateAdd(Date(Year(Today()), Month(Today()), 1), 1, TimeUnit.Months)
    },
    Filter(
        Requests,
        DueDate >= startDate &&
        DueDate < endDate
    )
)
```

## Last 30 Days

```powerfx
With(
    {
        startDate: DateAdd(Today(), -30, TimeUnit.Days),
        endDate: DateAdd(Today(), 1, TimeUnit.Days)
    },
    Filter(
        Requests,
        Created >= startDate &&
        Created < endDate
    )
)
```

## Optional Date Range Controls

```powerfx
With(
    {
        fromDate: dpFrom.SelectedDate,
        toDateExclusive: DateAdd(dpTo.SelectedDate, 1, TimeUnit.Days)
    },
    Filter(
        Requests,
        (IsBlank(fromDate) || DueDate >= fromDate) &&
        (IsBlank(dpTo.SelectedDate) || DueDate < toDateExclusive)
    )
)
```

## Notes

- Verify delegation for the connector and date column type.
- Avoid `Year(DataSourceColumn)`, `Month(DataSourceColumn)`, or `DateDiff(DataSourceColumn, ...)` in large remote filters.
- For DateTime columns, use an exclusive end date to include the full final day.
