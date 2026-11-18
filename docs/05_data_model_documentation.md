# Final Analytical Data Model

## Design decision

The final architecture is a **real-data star schema** for the 2019–May 2026 offshore window. The repaired V3 allocation fact is authoritative. Current well/field master attributes are conformed into dimensions, real interventions form a second event fact, and field-company ownership is represented by a genuine many-to-many bridge. ESP condition-monitoring tables form a dedicated equipment domain linked to the conformed well and calendar dimensions.

## Final tables

| table_name | table_type | business_purpose | grain | primary_key | foreign_keys | row_count | column_count |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dim_Date | Dimension | Daily calendar for monthly production and daily interventions | One row per calendar day | date_key |  | 2708 | 17 |
| Dim_State | Dimension | Conformed state | One row per state | state_key |  | 9 | 5 |
| Dim_Basin | Dimension | Conformed offshore basin | One row per basin | basin_key |  | 10 | 7 |
| Dim_Field | Dimension | Production field enriched with current contract/master attributes | One row per production field | field_key | basin_key; state_key; field_operator_key | 135 | 27 |
| Dim_Well | Dimension | Production well enriched with current ANP status snapshot | One row per production well plus unknown | well_key | current_operator_key | 2038 | 35 |
| Dim_Facility | Dimension | Production facility | One row per facility | facility_key |  | 219 | 8 |
| Dim_Company | Dimension | Company/operator/concessionaire | One row per normalized company | company_key |  | 60 | 5 |
| Fact_Monthly_Activity | Fact | Base production/injection allocation activity | One row per source allocation record | allocation_record_key | date_key; state_key; basin_key; field_key; well_key; facility_key | 138358 | 51 |
| Fact_Well_Month | Fact | Well-month production/injection aggregate | One row per well per month | well_month_key | date_key; well_key; primary_field_key; primary_facility_key | 98584 | 42 |
| Fact_Intervention | Fact | Real offshore intervention events | One row per intervention event | intervention_event_key | well_key; field_key; basin_key; current_operator_key; operator_at_intervention_key; date keys | 2840 | 23 |
| Bridge_Field_Concessionaire | Bridge | Many-to-many field ownership participation | One row per production field-company participation | field_company_bridge_key | field_key; company_key | 166 | 13 |
| Agg_Well_Month_Analytics | Aggregate | Calendar-aligned well-month features | One row per well per calendar month | well_month_analytics_key | date_key; well_key; primary field/facility keys | 106065 | 62 |
| Agg_Field_Month | Aggregate | Field-month KPI panel | One row per field per month | field_key + date_key | field_key; basin_key; state_key; date_key | 7506 | 33 |
| Agg_Facility_Month | Aggregate | Facility-month KPI panel | One row per facility per month | facility_key + date_key | facility_key; date_key | 7083 | 32 |
| Agg_Annual_KPI | Aggregate | Annual executive KPIs | One row per year | year |  | 8 | 29 |

## Measures and additivity

- **Additive:** oil, condensate, gas, water, injection volumes, intervention duration, allocation counts.
- **Semi-additive:** active-well counts and inventory counts may be summed across entities but not blindly across time.
- **Non-additive:** water cut, water-oil ratio, gas-oil ratio, shares, ranks, percentages, rolling averages, and participation percentages. Recalculate them from additive numerators/denominators at the required grain.

## Dimension behavior

- `Dim_Well` and `Dim_Field` contain **current snapshot enrichment**. If historical status/operator analysis becomes required, implement SCD Type 2 with effective dates. Current snapshot fields are excluded from historical ML unless they were known at the prediction date.
- `Dim_Company` is conformed across well status, field master, intervention operators, and concessionaires using normalized business keys.
- `Dim_Date` is daily so both monthly production dates and daily intervention events share one conformed calendar.

## Relationship validation

| parent_table | parent_key | child_table | child_key | cardinality_parent_to_child | match_percentage_non_null | orphan_child_rows | child_null_foreign_keys | validation_status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Dim_Date | date_key | Fact_Monthly_Activity | date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_State | state_key | Fact_Monthly_Activity | state_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Basin | basin_key | Fact_Monthly_Activity | basin_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Field | field_key | Fact_Monthly_Activity | field_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Well | well_key | Fact_Monthly_Activity | well_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Facility | facility_key | Fact_Monthly_Activity | facility_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Date | date_key | Fact_Well_Month | date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Well | well_key | Fact_Well_Month | well_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Field | field_key | Fact_Well_Month | primary_field_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Facility | facility_key | Fact_Well_Month | primary_facility_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Date | date_key | Fact_Intervention | intervention_start_date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Date | date_key | Fact_Intervention | intervention_end_date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Well | well_key | Fact_Intervention | well_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Field | field_key | Fact_Intervention | field_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Basin | basin_key | Fact_Intervention | basin_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Company | company_key | Fact_Intervention | current_operator_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Company | company_key | Fact_Intervention | operator_at_intervention_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Field | field_key | Bridge_Field_Concessionaire | field_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Company | company_key | Bridge_Field_Concessionaire | company_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Date | date_key | Agg_Well_Month_Analytics | date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Well | well_key | Agg_Well_Month_Analytics | well_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Date | date_key | Agg_Field_Month | date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Field | field_key | Agg_Field_Month | field_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Date | date_key | Agg_Facility_Month | date_key | 1:M | 100.0 | 0 | 0 | VALID |
| Dim_Facility | facility_key | Agg_Facility_Month | facility_key | 1:M | 100.0 | 0 | 0 | VALID |

## Coverage limitations

- `Fact_Intervention` retains all 2,840 offshore events. Of these, 2,161 match the modern production population and 679 map to the explicit unknown well member because those wells have no 2019–2026 production record in the selected fact.
- Current well status matches 1,958 of 2,037 production wells (96.1%).
- Field master/ownership covers 108 of 134 production fields (80.6%); all matched field ownership totals reconcile to 100%.
- 2026 is partial through May and must not be compared with a full year without equal-month filtering.

## Physical relationship notation

```text
Dim_Date[DateKey]                 1 |-- * Fact_Monthly_Activity[DateKey]
Dim_Well[WellKey]                 1 |-- * Fact_Monthly_Activity[WellKey]
Dim_Field[FieldKey]               1 |-- * Fact_Monthly_Activity[FieldKey]
Dim_Facility[FacilityKey]         1 |-- * Fact_Monthly_Activity[FacilityKey]
Dim_State[StateKey]               1 |-- * Fact_Monthly_Activity[StateKey]
Dim_Basin[BasinKey]               1 |-- * Fact_Monthly_Activity[BasinKey]
Dim_Well[WellKey]                 1 |-- * Fact_Well_Month[WellKey]
Dim_Well[WellKey]                 1 |-- * Fact_Intervention[WellKey]
Dim_Company[CompanyKey]           1 |-- * Fact_Intervention[OperatorKeys]
Dim_Field[FieldKey]               1 |-- * Bridge_Field_Concessionaire[FieldKey]
Dim_Company[CompanyKey]           1 |-- * Bridge_Field_Concessionaire[CompanyKey]
```
