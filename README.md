# ANP Offshore Production & ESP Analytics

DigiLians graduation project covering Brazilian offshore oil and gas production, well interventions, and an electric submersible pump (ESP) risk prototype.

**[Open the interactive dashboard](https://ahmed4320ah-ux.github.io/anp-offshore-analytics/)** · [Power BI report](power-bi/) · [Star schema](docs/05_star_schema.md) · [Pipeline source](source/v11.1/) · [Data samples](data/samples/)

## Project scope

| Component | Coverage |
| --- | --- |
| Web dashboard · V11.2 | Nine sections covering production, injection, fields and facilities, interventions, saved ESP risk results, model governance, and data exploration |
| Power BI | Six pages covering production, well risk, and four offshore regions |
| Data engineering · V11.1 | Python ETL, dimensional modeling, source reconciliation, and temporal model evaluation |

- Production data cover January 2019 to May 2026 and contain 138,358 allocation rows across 2,037 production wells.
- The intervention fact contains 2,840 events. The pipeline preserves 679 valid events without a matched production well using an unknown dimension member.
- The saved ESP prototype scored 423 equipment records for the May 2026 snapshot and flagged 162 records at its configured threshold. These are model outputs on synthetic ESP condition and reliability data.

## Data and models

Public [ANP production and intervention data](https://www.gov.br/anp/) underpin the historical analysis. ESP equipment, sensor, maintenance, and failure records are **synthetic** and use documented engineering assumptions. See the [ESP operating assumptions](docs/ESP_Operating_Assumptions.csv) and [dataset comparison](docs/V11_ESP_NUMERIC_EQUIVALENCE.csv).

The saved XGBoost prototype scores equipment failure risk over 90 days. Intervention risk and next-month oil production have model-ready datasets. The release contains exploratory baselines for those tasks and only deploys saved scores for the ESP prototype. This demonstration is for portfolio review and research.

## Releases and reproducibility

The live dashboard serves the **V11.2** static release with added labels identifying the synthetic ESP data. It displays saved results. The [curated V11.1 source](source/v11.1/) shows the pipeline and dashboard builder. A full V11.1 rebuild requires the matching datasets, model artifacts, and baseline files from the original runtime package. The samples here illustrate the schema. V11.2 HTML and its [release notes](docs/web-release/RELEASE_NOTES_V11.md) are included.