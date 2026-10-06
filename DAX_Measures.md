# Core DAX measures

These formulas were supplied by Laurent Holleville. Formatting is standardized; calculation logic is preserved.

## Base measures

```DAX
Total transactions = COUNTROWS(FactOperations)

Completed transactions =
CALCULATE(
    [Total transactions],
    FactOperations[status] = "Completed"
)

Average Processing Time =
AVERAGE(FactOperations[ProcessingTimeHours])
```

## Operational KPIs

```DAX
Service Level % =
DIVIDE(
    CALCULATE(
        [Total transactions],
        FactOperations[ProcessingTimeHours] <= 24
    ),
    [Total transactions]
)

Quality rate % =
DIVIDE(
    CALCULATE(
        [Total transactions],
        FactOperations[QualityPassed] = 1
    ),
    [Total transactions]
)

Backlog =
CALCULATE(
    [Total transactions],
    FactOperations[BacklogAgeDays] > 0
)
```

Service level includes outstanding transactions. The author confirms that all outstanding cases have populated processing times. Quality's denominator includes all transactions, not only quality-assessed or completed transactions. Backlog counts rows with positive backlog age in context; its interpretation depends on the dataset's date and snapshot conventions.

## Previous-month label and measures

```DAX
Previous Month Year =
FORMAT(
    EDATE(MAX(DimDate[Date]), -1),
    "MMMM YYYY"
)

PM Transactions =
CALCULATE(
    [Total transactions],
    DATEADD(DimDate[Date], -1, MONTH)
)

PM Service Level =
CALCULATE(
    [Service Level %],
    DATEADD(DimDate[Date], -1, MONTH)
)

PM Quality =
CALCULATE(
    [Quality rate %],
    DATEADD(DimDate[Date], -1, MONTH)
)

PM Processing time =
CALCULATE(
    [Average Processing Time],
    DATEADD(DimDate[Date], -1, MONTH)
)

PM cust sat =
CALCULATE(
    [Average customer satisfaction],
    DATEADD(DimDate[Date], -1, MONTH)
)

PM Backlog =
CALCULATE(
    [Backlog],
    DATEADD(DimDate[Date], -1, MONTH)
)
```

The exact underlying `[Average customer satisfaction]` formula was not supplied. The PM formula above preserves its supplied reference. These measures shift the active date context; they do not independently select the latest month.

## QA confirmed by the author

| KPI | June 2026 | May 2026 |
|---|---:|---:|
| Transactions | 451 | 534 |
| Service level | 80.5% | 82.2% |
| Processing time | 17.1 hours | 15.9 hours |
| Customer satisfaction | 4.4 | 4.3 |
| Quality rate | 93.1% | 92.3% |
| Backlog | 49 | 54 |

Reported QA is distinct from independently executing the formulas during documentation review.
