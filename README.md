# ANP Offshore Production & ESP Analytics

DigiLians graduation project covering Brazilian offshore oil and gas production, well interventions, and an electric submersible pump (ESP) risk prototype.

**[Open the interactive dashboard](https://ahmed4320ah-ux.github.io/anp-offshore-analytics/)** · [Download Power BI report](https://github.com/ahmed4320ah-ux/anp-offshore-analytics/releases/download/v11.2.0/ANP_Offshore_Dashboard.pbix) · [Star schema](docs/05_star_schema.md) · [Download Python pipeline and sample data](https://github.com/ahmed4320ah-ux/anp-offshore-analytics/releases/download/v11.2.0/ANP_Offshore_Pipeline_V11_1.zip)

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

The XGBoost prototype scores equipment failure risk over 90 days. Intervention risk and next-month oil production have model-ready datasets and exploratory baselines. This release displays the saved ESP scores and serves as a portfolio and research demonstration.

## Reports and reproducibility

The live dashboard serves the **V11.2** static release and displays saved results. The **V11.1** archive contains the curated Python ETL, dashboard builders, dimensional model, validation notes, and sample CSVs. A full V11.1 rebuild requires the matching datasets, model artifacts, and baseline files from the original runtime package.

The PBIX opens in Power BI Desktop and contains six pages. Refreshing the report requires the original sources and valid local connections.

[Data model](docs/05_data_model_documentation.md) · [Data dictionary](docs/Final_Data_Dictionary_v3.csv) · [Validation report](docs/10_validation_report.md) · [Release notes and downloads](https://github.com/ahmed4320ah-ux/anp-offshore-analytics/releases/tag/v11.2.0)
