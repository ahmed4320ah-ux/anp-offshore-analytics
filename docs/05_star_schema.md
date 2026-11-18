# Star Schema

The model uses conformed date, state, basin, field, well, facility, and operator dimensions. Production and intervention facts keep their individual grains.

```mermaid
erDiagram
    DIM_DATE ||--o{ FACT_MONTHLY_ACTIVITY : dates
    DIM_STATE ||--o{ FACT_MONTHLY_ACTIVITY : states
    DIM_BASIN ||--o{ FACT_MONTHLY_ACTIVITY : basins
    DIM_FIELD ||--o{ FACT_MONTHLY_ACTIVITY : fields
    DIM_WELL ||--o{ FACT_MONTHLY_ACTIVITY : wells
    DIM_FACILITY ||--o{ FACT_MONTHLY_ACTIVITY : facilities
    DIM_DATE ||--o{ FACT_INTERVENTION : dates
    DIM_WELL ||--o{ FACT_INTERVENTION : wells
    DIM_FIELD ||--o{ FACT_INTERVENTION : fields
    DIM_BASIN ||--o{ FACT_INTERVENTION : basins
    DIM_COMPANY ||--o{ FACT_INTERVENTION : operators
```

The V11.1 source archive includes the complete dimensional model and validation notes.
