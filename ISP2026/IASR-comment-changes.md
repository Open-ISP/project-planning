# Comment changes and fixes for workbook 7.8

Table below is just intended to more clearly show the change in outputs from fixes to the comment sanitizers in the update to the workbook parser for v7.8 of the workbook. 

The table shows the example output filename, the column that has changed - the current/existing output from the parser, the new output from the dix (and the the original text to check / confirm the results)

| filename | column | current | updated | original text |
|---|---|---|---|---|
| `flow_path_augmentation_options_CSA-NSA` | Indicative cost estimate ($2025, $ million) | `1749.5$23 million of this amount relates to approved early works costs…` | `1749.5` | 1,749.5 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 2… |
| `flow_path_augmentation_options_CSA-NSA` | Indicative cost estimate ($2025, $ million) | `2068$23 million of this amount relates to approved early works costs a…` | `2068.0` | 2,068 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 202… |
| `flow_path_augmentation_options_CSA-NSA` | Indicative cost estimate ($2025, $ million) | `3879$23 million of this amount relates to approved early works costs a…` | `3879.0` | 3,879 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 202… |
| `flow_path_augmentation_options_CSA-NSA` | Indicative cost estimate ($2025, $ million) | `2880$23 million of this amount relates to approved early works costs a…` | `2880.0` | 2,880 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 202… |
| `flow_path_augmentation_options_CSA-NSA` | Indicative cost estimate ($2025, $ million) | `1459$23 million of this amount relates to approved early works costs a…` | `1459.0` | 1,459 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 202… |
| `flow_path_augmentation_options_TAS-SEV` | Notional transfer level increase (MW) …_Reverse direction | `750[footnote14]` | `750` | 750[footnote14] |
| `flow_path_augmentation_options_TAS-SEV` | Indicative cost estimate ($2025, $ million) | `4758$534 million, in $2023, of this amount relates to approved early w…` | `4758` | 4758 (Marinus Link Pty Ltd and TasNetworks have advised that $534 million, in $2023, of this amount relates to approved early works and other incurred costs that should be excluded from the cost estim… |
| `flow_path_augmentation_options_TAS-SEV` | Notional transfer level increase (MW) …_Reverse direction | `750[footnote15]` | `750` | 750[footnote15] |
| `flow_path_augmentation_options_TAS-SEV` | Indicative cost estimate ($2025, $ million) | `2626$51 million, in $2023, of this amount relates to approved early wo…` | `2626` | 2626 (Marinus Link Pty Ltd has advised that $51 million, in $2023, of this amount relates to approved early works and other incurred costs that should be excluded from the cost estimate for the 2026 I… |
| `flow_path_augmentation_options_TAS-SEV` | Notional transfer level increase (MW) …_Reverse direction | `750[footnote15]` | `750` | 750[footnote15] |
| `flow_path_augmentation_options_WNV-SNSW` | Indicative cost estimate ($2025, $ million) | `7035$565 million of this amount relates to approved early works and ot…` | `7035` | 7035 (Transgrid has advised $565 million of this amount relates to approved early works and other incurred costs that should be excluded from the total cost estimate of $7,600 million for the 2026 ISP… |
| `gas_system_properties_pipelines` | Capacity (TJ per day) | `350362 (Winter)` | `350` | 350 (Summer) / 362 (Winter) |
| `rez_augmentation_options_NSW` | Expected cost ($2025 million) | `607$601 in $2024)` | `607` | 607 (based on a provided cost of $601 in $2024) |
| `rez_augmentation_options_NSW` | Expected cost ($2025 million) | `126$125 in $2024)` | `126` | 126 (based on a provided cost of $125 in $2024) |
| `rez_augmentation_options_NSW` | Expected cost ($2025 million) | `283$280 in $2024)` | `283` | 283 (based on a provided cost of $280 in $2024) |
| `rez_augmentation_options_NSW` | Expected cost ($2025 million) | `953$943 in $2024)` | `953` | 953 (based on a provided cost of $943 in $2024) |
| `rez_augmentation_options_NSW` | Expected cost ($2025 million) | `274$271 in $2024)` | `274` | 274 (based on a provided cost of $271 in $2024) |
| `rez_augmentation_options_NSW` | Expected cost ($2025 million) | `309$306 in $2024)` | `309` | 309 (based on a provided cost of $306 in $2024) |
| `rez_augmentation_options_SA` | Expected cost ($2025 million) | `179` | `179.0` | 179 |
| `rez_augmentation_options_SA` | Expected cost ($2025 million) | `925$23 million of this amount relates to approved early works costs an…` | `925.0` | 925 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 2026… |
| `rez_augmentation_options_SA` | Expected cost ($2025 million) | `1429$23 million of this amount relates to approved early works costs a…` | `1429.0` | 1429 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 2026… |
| `rez_augmentation_options_SA` | Expected cost ($2025 million) | `1769$23 million of this amount relates to approved early works costs a…` | `1769.0` | 1769 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 2026… |
| `rez_augmentation_options_SA` | Expected cost ($2025 million) | `3539$23 million of this amount relates to approved early works costs a…` | `3539.0` | 3539 (ElectraNet has advised that approximately $23 million of this amount relates to approved early works costs and other incurred costs. These already incurred costs have been excluded from the 2026… |
| `rez_augmentation_options_SA` | Expected cost ($2025 million) | `484` | `484.0` | 484 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `600` | `600.0` | 600 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `600` | `600.0` | 600 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `250` | `250.0` | 250 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `500` | `500.0` | 500 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `2000` | `2000.0` | 2000 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `2000` | `2000.0` | 2000 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `1000` | `1000.0` | 1000 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `400` | `400.0` | 400 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `1500` | `1500.0` | 1500 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `1750` | `1750.0` | 1750 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `2900` | `2900.0` | 2900 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `1800` | `1800.0` | 1800 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `1000` | `1000.0` | 1000 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `2800` | `2800.0` | 2800 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `2750` | `2750.0` | 2750 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `1380` | `1380.0` | 1380 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `4600: 3000)` | `4600.0` | 4600 (V8: 3,000) |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `200` | `200.0` | 200 |
| `rez_augmentation_options_VIC` | Additional network capacity (MW) | `530` | `530.0` | 530 |
