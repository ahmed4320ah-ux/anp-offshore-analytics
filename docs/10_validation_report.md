# Validation Report

## Outcome

- Star-schema checks: **58 total; 57 PASS; 1 WARN; 0 FAIL**.
- Source integrity: **120 of 120 physical source files rehashed; 0 mismatches** after output preparation.
- Relationship checks: **25 valid of 25**, with zero orphan FK rows and unique parent keys.
- All dimension/fact/bridge/aggregate primary or composite keys are non-null and unique at the documented grain.
- All selected production/injection measure sums reconcile exactly to the authoritative V3 facts.
- ML dataset checks have **0 failures**.

## Expected warning

| test_name | table_name | actual | expected | status | evidence |
| --- | --- | --- | --- | --- | --- |
| intervention_natural_well_match_coverage | Fact_Intervention | 2161 | 2840 | WARN | 679 valid offshore intervention events belong to wells outside the modern production-well population and use the explicit unknown member. |

The warning is not data loss: all 2,840 real offshore intervention events are retained. The 679 events without a modern production-well match use `well_key=0`; only matched events are used to create the ML target.

## Table row counts and grains

| table_name | table_type | grain | primary_key | row_count | column_count |
| --- | --- | --- | --- | --- | --- |
| Dim_Date | Dimension | One row per calendar day | date_key | 2708 | 17 |
| Dim_State | Dimension | One row per state | state_key | 9 | 5 |
| Dim_Basin | Dimension | One row per basin | basin_key | 10 | 7 |
| Dim_Field | Dimension | One row per production field | field_key | 135 | 27 |
| Dim_Well | Dimension | One row per production well plus unknown | well_key | 2038 | 35 |
| Dim_Facility | Dimension | One row per facility | facility_key | 219 | 8 |
| Dim_Company | Dimension | One row per normalized company | company_key | 60 | 5 |
| Fact_Monthly_Activity | Fact | One row per source allocation record | allocation_record_key | 138358 | 51 |
| Fact_Well_Month | Fact | One row per well per month | well_month_key | 98584 | 42 |
| Fact_Intervention | Fact | One row per intervention event | intervention_event_key | 2840 | 23 |
| Bridge_Field_Concessionaire | Bridge | One row per production field-company participation | field_company_bridge_key | 166 | 13 |
| Agg_Well_Month_Analytics | Aggregate | One row per well per calendar month | well_month_analytics_key | 106065 | 62 |
| Agg_Field_Month | Aggregate | One row per field per month | field_key + date_key | 7506 | 33 |
| Agg_Facility_Month | Aggregate | One row per facility per month | facility_key + date_key | 7083 | 32 |
| Agg_Annual_KPI | Aggregate | One row per year | year | 8 | 29 |

## Relationship integrity

| relationship_name | cardinality_parent_to_child | child_null_foreign_keys | orphan_child_rows | match_percentage_non_null | validation_status |
| --- | --- | --- | --- | --- | --- |
| Date to monthly activity | 1:M | 0 | 0 | 100.0 | VALID |
| State to monthly activity | 1:M | 0 | 0 | 100.0 | VALID |
| Basin to monthly activity | 1:M | 0 | 0 | 100.0 | VALID |
| Field to monthly activity | 1:M | 0 | 0 | 100.0 | VALID |
| Well to monthly activity | 1:M | 0 | 0 | 100.0 | VALID |
| Facility to monthly activity | 1:M | 0 | 0 | 100.0 | VALID |
| Date to well-month | 1:M | 0 | 0 | 100.0 | VALID |
| Well to well-month | 1:M | 0 | 0 | 100.0 | VALID |
| Primary field to well-month | 1:M | 0 | 0 | 100.0 | VALID |
| Primary facility to well-month | 1:M | 0 | 0 | 100.0 | VALID |
| Start date to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| End date to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| Well to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| Field to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| Basin to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| Current operator to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| Event operator to intervention | 1:M | 0 | 0 | 100.0 | VALID |
| Field to ownership bridge | 1:M | 0 | 0 | 100.0 | VALID |
| Company to ownership bridge | 1:M | 0 | 0 | 100.0 | VALID |
| Date to well analytics | 1:M | 0 | 0 | 100.0 | VALID |
| Well to well analytics | 1:M | 0 | 0 | 100.0 | VALID |
| Date to field-month | 1:M | 0 | 0 | 100.0 | VALID |
| Field to field-month | 1:M | 0 | 0 | 100.0 | VALID |
| Date to facility-month | 1:M | 0 | 0 | 100.0 | VALID |
| Facility to facility-month | 1:M | 0 | 0 | 100.0 | VALID |

## Primary ML dataset

- Rows: **75,032**; target positives: **1,454 (1.94%)**.
- Observation IDs unique: `True`.
- Target missing: `0`.
- Temporal range: `2019-12-01` to `2026-01-01`.
- Missing feature values are deliberately retained for TRAIN-only imputation. No full-dataset imputation/scaling was performed.

### Highest missingness columns

| column | missing_pct |
| --- | --- |
| days_since_last_intervention | 76.49802750826315 |
| wor_change_3m | 59.28270604542062 |
| water_cut_change_6m | 58.4790489391193 |
| water_cut_change_3m | 57.41550271883996 |
| gor_change_3m | 57.41017166009169 |
| oil_change_1m_pct | 56.26266126452714 |
| oil_change_3m_pct | 56.2240110886022 |
| water_cut_change_1m | 55.87882503465188 |
| water_oil_ratio | 55.87349397590361 |
| oil_change_6m_pct | 55.801524682802004 |
| oil_change_12m_pct | 55.73355368376159 |
| gas_oil_ratio_m3_per_m3 | 53.84102782812666 |

### Class balance by split

| model | check | actual | expected | status |
| --- | --- | --- | --- | --- |
| primary_model | target_positive_rate_pct::OOT | 0.834492 | review rare-event balance | PASS |
| primary_model | target_positive_rate_pct::TEST | 1.73731 | review rare-event balance | PASS |
| primary_model | target_positive_rate_pct::TRAIN | 1.953953 | review rare-event balance | PASS |
| primary_model | target_positive_rate_pct::VALIDATION | 2.137506 | review rare-event balance | PASS |

## Secondary ML dataset

- Rows: **75,181**; target missing: `0`.
- Observation IDs unique: `True`.
- Temporal range: `2019-12-01` to `2026-04-01`.
- Conditional target-zero conversion was applied only when the next month is observed and explicitly not production-active; null is not globally treated as zero.

### Highest missingness columns

| column | missing_pct |
| --- | --- |
| wor_change_3m | 58.21018608425001 |
| water_cut_change_6m | 57.39881086976762 |
| water_cut_change_3m | 56.30012902196033 |
| gor_change_3m | 56.294808528750615 |
| oil_change_3m_pct | 55.20809779066519 |
| oil_change_1m_pct | 55.145581995450975 |
| oil_change_6m_pct | 54.85295486891635 |
| oil_change_12m_pct | 54.84231388249691 |
| water_oil_ratio | 54.791769197004555 |
| water_cut_change_1m | 54.694670195927166 |
| gas_oil_ratio_m3_per_m3 | 52.728747954935415 |
| water_cut_pct | 52.71943709181841 |

## Leakage controls

- Future intervention fields and target-window events are excluded from features.
- Current well status/lift/operator snapshots are excluded from historical ML.
- Rolling and lag features are backward-looking; V3 reconciliation reports zero temporal semantic errors.
- Preprocessing must be fit on TRAIN only; the delivered CSVs preserve raw missingness for that reason.
- IDs are retained only for grouping, traceability, and dashboard output, not as unrestricted numeric predictors.

## Training status

No final model was trained. The primary exploratory logistic baseline was not run because the bundled preparation runtime lacks `scikit-learn`; this does not affect the model-ready dataset. The secondary persistence/mean baselines were computed directly as feasibility references and are explicitly marked exploratory.
