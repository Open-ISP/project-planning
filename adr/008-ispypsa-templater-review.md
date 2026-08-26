# ISPyPSA Input Tables: Node-Unified Restructuring Plan

## Purpose
This document proposes a node-unified input-table structure and table relationships, plus the data regularization needed to support that structure.

Primary goals:
- Improve conciseness, readability, and intuitiveness.
- Reduce schema noise from unit-heavy and mapping-heavy columns.
- Shift time-varying facts from wide format (many year columns) to long format (many rows).
- Unify subregions and REZs into a single spatial concept: `node`.
- Unify transmission handling through a single transmission_path model.

The covered input set is consolidated from ~30 tables to 22 tables.

Current tables consolidated:
- `sub_regions`, `nem_regions`, `renewable_energy_zones` -> `network_nodes`
- `flow_paths` + REZ connections -> `network_transmission_paths`
- (new) -> `network_transmission_path_limits`
- `flow_path_expansion_costs`, `rez_transmission_expansion_costs` -> `network_transmission_path_expansion_costs`
- `renewable_energy_zones` (resource limit columns) -> `node_resource_limits`
- `ecaa_generators` -> `assets_existing_generators`
- `ecaa_batteries` -> `assets_existing_storage`
- `new_entrant_generators` -> `assets_new_generators`
- `new_entrant_batteries` -> `assets_new_storage`
- `coal_prices`, `gas_prices`, `liquid_fuel_prices`, `biomass_prices`, `biomethane_prices`, `hydrogen_prices` -> `costs_fuel_prices`
- `new_entrant_build_costs` -> `costs_new_entrant_build`
- `new_entrant_wind_and_solar_connection_costs`, `new_entrant_non_vre_connection_costs` -> `costs_connection`
- `gpg_emissions_reduction_h2`, `gpg_emissions_reduction_biomethane` -> `emissions_reduction`
- `seasonal_ratings` -> `reliability_seasonal_ratings`
- `full_outage_forecasts`, `partial_outage_forecasts` -> `reliability_outage_forecasts`
- `renewable_share_targets`, `powering_australia_plan`, `renewable_generation_targets`, `technology_capacity_targets` -> `policy_targets`
- `policy_generator_types` -> `policy_eligible_technologies`
- `custom_constraints_lhs`, `custom_constraints_rhs` -> `constraints_lhs`, `constraints_rhs`

---

## Cross-cutting design rules

### A. Long format for time-series facts
Use `year` (integer) instead of year-per-column layouts.

Affected current tables (all wide-format with financial year columns):
- `coal_prices`, `gas_prices`, `liquid_fuel_prices`, `biomass_prices`, `biomethane_prices`, `hydrogen_prices`
- `new_entrant_build_costs`
- `new_entrant_wind_and_solar_connection_costs`
- `flow_path_expansion_costs`, `rez_transmission_expansion_costs`
- `full_outage_forecasts`, `partial_outage_forecasts`
- `gpg_emissions_reduction_h2`, `gpg_emissions_reduction_biomethane`

### B. Remove units from column names
Use plain names such as `cost`, `price`, `capacity`, `fom`, and `vom`.
Document units in schema docs/metadata.

### C. Canonical naming for columns
Use consistent entity naming:
- `name` for asset names
- `technology` for technology labels
- `node_id` for all location keys
- `region_id` retained only for rollups or validation

Canonicalize values across tables to remove dedicated mapping columns.

### D. Canonical naming for tables (family prefixes)
Use consistent table-family prefixes:
- `network_*` for topology and interconnector tables
- `node_*` for node attributes and resource limits
- `assets_existing_*` and `assets_new_*` for unit definition tables
- `costs_*` for build, fuel, connection, and expansion costs
- `reliability_*` for dynamic reliability-related technical profiles
- `policy_*` for policy targets and eligibility
- `constraints_*` for custom network and operational constraints

### E. Canonical column order
Use this column order in every table:
1. Entity keys (`name`, `technology`, `fuel_type`, etc.)
2. Scope/location keys (`node_id`, `region_id`, `timeslice`, `direction`, `outage_type`, etc.)
3. Time key (`year`) when applicable
4. Measure columns (`cost`, `price`, `capacity`, `rate`, etc.)
5. Optional provenance/debug columns (`source_option`, `source_id`) only when needed

### F. Null representation in CSV
Use one null style only: empty CSV field (`""` in quoted CSV, otherwise blank between commas).
- Do not write `NaN`, `NULL`, or mixed null markers.

### G. Non-VRE connection cost year regularization
Non-VRE connection costs are currently static (no year dimension). To regularize the `costs_connection.csv` table so that all rows have a `year` column, the templater duplicates static non-VRE connection costs across all model years present in the VRE connection cost data. This ensures every row in `costs_connection.csv` has a `year` value and the translator can perform a uniform `node_id + technology + year` join without special-casing.

### H. Resource type canonicalization (optional)

The current templater uses ISP resource quality codes (`isp_resource_type`) to distinguish VRE sub-technologies. These codes originate from AEMO's workbook naming and are used in generator naming, VRE build limit constraints, and resource limit lookups.

This plan proposes canonicalizing these codes to human-readable `resource_type` values using snake_case. The mapping is:

| Current ISP code | Proposed `resource_type` | Description |
|------------------|--------------------------|-------------|
| `WH` | `wind_high` | Onshore wind — high resource quality |
| `WM` | `wind_medium` | Onshore wind — medium resource quality |
| `WFX` | `wind_offshore_fixed` | Offshore wind — fixed foundation |
| `WFL` | `wind_offshore_floating` | Offshore wind — floating foundation |
| `SAT` | `solar` | Large-scale solar PV |
| `CST` | `solar_thermal` | Concentrating solar thermal |

**Affected locations:**
- `assets_new_generators.csv` — `resource_type` column (currently `isp_resource_type` with ISP codes)
- `node_resource_limits.csv` — `resource_type` column (currently derived from REZ table column names)
- Generator unique naming — currently `{technology}_{isp_code}_{node_id}`, would become `{technology}_{resource_type}_{node_id}`
- Translator VRE build limit constraint groups (`_VRE_BUILD_LIMIT_CUSTOM_CONSTRAINT_GROUPS` in `mappings.py`) — filters generators by `isp_resource_type` values; must be updated to match new names

**Note:** This canonicalization is **optional**. The current ISP codes (`WH`, `WM`, `SAT`, etc.) are concise and unambiguous. Retaining them avoids changes to the translator's constraint configuration and generator naming. The trade-off is readability: `wind_high` is more self-documenting than `WH` for new users of the schema. Either approach is acceptable — the key requirement is consistency between `assets_new_generators.resource_type` and `node_resource_limits.resource_type`.

---

## 1. Network Topology (`network_*`)

### 1a. `network_nodes.csv`

```text
node_id  node_type  region_id  subregion_id
NNSW     subregion  NSW        NNSW
N1       rez        NSW        NNSW
Q1       rez        QLD        SQ
```

Source tables: `sub_regions`, `nem_regions`, `renewable_energy_zones`

Notes:
- `node_type`, `region_id`, and `subregion_id` are for traceability and results formatting only; all table mappings are based off `node_id`
- `node_type`: `subregion`, `region`, `rez`. This is derivable from the data (`node_id == subregion_id` implies zone) but included for readability.

### 1b. `network_transmission_paths.csv`

```text
path_id   node_from  node_to  carrier  expansion_option  allowed_expansion
NNSW-SQ   NNSW       SQ       AC       Opt2              300
CQ-NQ     CQ         NQ       AC       Opt1              500
N1-NNSW   N1         NNSW     AC       Opt1              166
```

Source tables: `flow_paths` (sub-regional and regional), REZ connections from `renewable_energy_zones`

Notes:
- Single transmission path table for all connection types: inter-zonal flow paths, regional interconnectors, and REZ-to-zone connections
- Internal node transmission paths (e.g., REZ -> zone) are represented explicitly
- `expansion_option` and `allowed_expansion` define the optional expansion bundle per path
- The templater's least-cost option selection logic (augmentation options, preparatory activities, actionable ISP projects) remains in the templater; only the selected option is output here

### 1c. `network_transmission_path_limits.csv` (new)

```text
path_id   direction  timeslice        capacity
NNSW-SQ   forward    summer_typical   910
NNSW-SQ   reverse    summer_typical   930
NNSW-SQ   forward    peak_demand      685
NNSW-SQ   reverse    peak_demand      1165
NNSW-SQ   forward    winter           745
NNSW-SQ   reverse    winter           745
N1-NNSW   forward    summer_typical   171
```

Notes:
- Static limits sourced from the IASR `flow_path_transfer_capability` and `interconnector_transfer_capability` tables. These do not vary by year — the IASR provides only a single snapshot of capability approximations per timeslice.
- For REZ connections where the IASR provides no explicit transmission limit, the capacity is `NaN`. This indicates the REZ connection is constraint-modeled (its effective limit is enforced via custom constraints in `constraints_lhs/rhs` rather than a physical link capacity). The translator should assign a sufficiently large default capacity for these rows.

### 1d. `network_transmission_path_expansion_costs.csv`

```text
path_id   year  cost
NNSW-SQ   2028  4975164.94
NNSW-SQ   2029  4994562.23
N1-NNSW   2028  28214281.29
N1-NNSW   2029  28334436.97
```

Source tables: `flow_path_expansion_costs`, `rez_transmission_expansion_costs`

Notes:
- Merges two currently separate tables: sub-regional flow path expansion costs and REZ transmission expansion costs
- Both are currently in wide format with financial year columns; convert to long format
- `path_id` for flow paths matches the `path_id` in `network_transmission_paths.csv`
- `path_id` for REZ connections uses the `rez_id`-based path (e.g. `N1-NNSW`)
- Constraint group IDs (e.g. `NQ2`, `SEVIC1`) that appear in `rez_transmission_expansion_costs` but do not correspond to a physical path are retained with their constraint group ID as `path_id`; the translator uses these for custom constraint generator creation (see section 10)

---

## 2. Node Limits (`node_*`)

### 2a. `node_resource_limits.csv` (new)

```text
node_id  resource_type      limit_type  limit    resource_limit_penalty
N1       solar              generation  6385.0   288711.0
N1       wind               land_use    4755.0
N2       wind_high          generation  1800.0   288711.0
N2       wind_medium        generation  5600.0   288711.0
N2       wind_offshore_fixed  generation  0.0
```

Source table: `renewable_energy_zones` (resource/build limit columns)

Notes:
- `resource_type` is snake_case and canonicalized across all tables
- Resource limits are attached directly to `node_id`
- `limit_type` distinguishes two constraint semantics:
  - `generation`: per-resource-type limit (e.g. wind_high, solar). These are **soft constraints** when `resource_limit_penalty` is set — the translator creates dummy penalty generators to allow relaxation at a cost.
  - `land_use`: total wind or solar capacity limit per node. These are **hard caps** — no relaxation is permitted.
- `resource_limit_penalty` is the annualized penalty ($/MW) applied if a `generation` limit is exceeded
- `resource_limit_penalty` blank means no violation is allowed (hard cap); this applies to all `land_use` rows and to `generation` rows for offshore wind

---

## 3. Existing Units (`assets_existing_*`)

### 3a. `assets_existing_generators.csv`

```text
name            technology            node_id  fuel_type   fuel_price_node  fom   vom  heat_rate  capacity  commissioning_date  closure_year  minimum_load
Bayswater       "Steam Sub Critical"  NNSW     "Black Coal"  Bayswater      64.5  5.1  9.45       2715      1985-01-01          2033          250
Eraring         "Steam Sub Critical"  NNSW     "Black Coal"  Eraring        70.2  4.8  9.65       2880      1984-01-01          2032          260
"Darling Downs" CCGT                  SQ       Gas           "Darling Downs" 18.0  2.5  7.20       630       2010-07-01          2050          120
```

Changes:
- `generator -> name`
- `technology_type -> technology`
- `fuel_cost_mapping -> fuel_price_node` (optional)
- drop `status`
- replace `subregion_id`/`rez_id` with `node_id`
- unitless column names

### 3b. `assets_existing_storage.csv`

```text
name                  technology         node_id  fuel_type  fom    capacity  storage_hours  commissioning_date  closure_year  lifetime  charging_efficiency  discharging_efficiency
"Wallgrove BESS"      "Battery Storage"  SNW      Battery    30.69  50        2              2023-07-01          2043          20        91.1                 91.1
"Wandoan South BESS"  "Battery Storage"  SQ       Battery    31.20  100       4              2026-07-01          2046          20        90.5                 90.5
```

Changes:
- drop `status`
- replace `subregion_id`/`rez_id` with `node_id`
- unitless column names

Implementation note:
- fix templater mapping issue where `lifetime` can be non-numeric.

---

## 4. New Entrant Units (`assets_new_*`)

### 4a. `assets_new_generators.csv`

```text
name                      technology                resource_type   node_id  fuel_type  fom    vom    heat_rate  lcf  lifetime  minimum_stable_level
ocgt_small_gt_nnsw        "OCGT (small GT)"                         NNSW     Gas        13.47  12.83  10.19      100  40        0.0
large_scale_solar_pv_n1   "Large scale Solar PV"    solar           N1       Solar      18.18  0.00   0.00       101  30        0.0
wind_offshore_fixed_n10   "Wind - offshore (fixed)" offshore_fixed  N10      Wind       18.18  0.00   0.00       100  30        0.0
```

- Keep columns: `name, technology, resource_type, node_id, fuel_type, fom, vom, heat_rate, lcf, lifetime, minimum_stable_level`.
- Drop columns: `status, build_limit_*, fuel_cost_mapping, connection_cost_technology, connection_cost_rez/_region_id, minimum_load_mw`.
- Joins:
  - build costs: `technology + year`
  - connection costs: `node_id + year` (VRE) or `node_id + technology + year` (non-VRE)
  - fuel prices: precedence = node-specific, then `fuel_type + technology + node_id`, then `fuel_type`
  - node limits: `node_id + resource_type`
- Required regularization:
  - canonicalize `technology` labels across dependent tables
  - canonicalize `resource_type` using snake_case across all tables
  - normalize VRE connection locations to `node_id`
  - If new generator data is provided at `region_id` level, duplicate to all non-REZ nodes in the region so mapping to fuel and connection costs can be done on a `node_id` basis.
- Rationale for dropped columns:
  - legacy mapping columns are derivable from structural keys
  - build limits come from normalized node limit tables
  - lower bound is represented by `minimum_stable_level` (`p_min_pu`), so `minimum_load_mw` is unnecessary for new entrants

### 4b. `assets_new_storage.csv`

```text
name                      technology                       node_id  fuel_type  fom   storage_hours  lifetime  lcf  charging_efficiency  discharging_efficiency
battery_storage_1h_nnsw   "Battery Storage (1hr storage)"  NNSW     Battery    18.0  1              20        100  91.1                 91.1
battery_storage_4h_sq     "Battery Storage (4hr storage)"  SQ       Battery    19.2  4              20        100  90.8                 90.8
```

Changes:
- drop `status`
- drop `round_trip_efficiency`
- use `technology` as the technology descriptor
- replace `subregion_id`/`rez_id` with `node_id`

Relationship mapping notes:
- Build costs join on `technology + year` to `costs_new_entrant_build.csv`.
- Connection costs join on `node_id + year` (VRE) or `node_id + technology + year` (non-VRE).
- No battery-specific connection mapping columns are needed in the battery table.
- If new storage data is provided at `region_id` level, duplicate to all non-REZ nodes in the region so mapping to fuel and connection costs can be done on a `node_id` basis.

---

## 5. Optional Consolidation of Existing and New Assets

If preferred, consolidate existing and new asset tables into a single `assets.csv` with required `asset_scope` (`existing` or `new`) and `asset_type` columns (e.g., `generator`, `storage`) and the union of columns used across asset tables. Use nulls for non-applicable fields.

Example:

```text
asset_scope  asset_type  name                      technology                       resource_type   node_id  fuel_type     fuel_price_node  fom    vom    heat_rate  capacity  storage_hours  commissioning_date  closure_year  lifetime  lcf  charging_efficiency  discharging_efficiency  minimum_stable_level  minimum_load
existing     generator   Bayswater                 "Steam Sub Critical"                             NNSW     "Black Coal"  Bayswater        64.5   5.1    9.45       2715                     1985-01-01          2033                                                                                                250
existing     generator   Eraring                   "Steam Sub Critical"                             NNSW     "Black Coal"  Eraring          70.2   4.8    9.65       2880                     1984-01-01          2032                                                                                                260
existing     storage     "Wallgrove BESS"          "Battery Storage"                                SNW      Battery                        30.69                    50        2              2023-07-01          2043          20             91.1                 91.1
existing     storage     "Wandoan South BESS"      "Battery Storage"                                SQ       Battery                        31.20                    100       4              2026-07-01          2046          20             90.5                 90.5
new          generator   ocgt_small_gt_nnsw        "OCGT (small GT)"                                NNSW     Gas                            13.47  12.83  10.19                                                               40        100                                               0.0
new          generator   large_scale_solar_pv_n1   "Large scale Solar PV"           solar           N1       Solar                          18.18  0.00   0.00                                                                30        101                                               0.0
new          storage     battery_storage_1h_nnsw   "Battery Storage (1hr storage)"                  NNSW     Battery                        18.0                               1                                               20        100  91.1                 91.1
```

---

## 6. Cost and Price Tables (`costs_*`)

### 6a. `costs_fuel_prices.csv` (consolidated)

```text
fuel_type      technology          node_id  fuel_price_node    year  price
"Black Coal"                                Bayswater          2028  1.98
"Black Coal"                                Eraring            2028  9.69
Gas                                         "Darling Downs"    2028  10.30
Gas                                         "Condamine A"      2028  10.50
Hydrogen                                                       2028  42.90
Biomethane                                                     2028  15.30
"Liquid Fuel"                                                  2028  22.10
Biomass                                                        2028  6.50
Solar                                                          2028  0.00
```

Source tables: `coal_prices`, `gas_prices`, `liquid_fuel_prices`, `biomass_prices`, `biomethane_prices`, `hydrogen_prices`

Supported row scopes:
- generator-specific (`fuel_price_node` set) — all coal and gas generators that currently have per-generator price trajectories retain them via `fuel_price_node`. The `fuel_price_node` value is the generator name (matching the `fuel_price_node` column in `assets_existing_generators.csv`).
- fuel-global (`fuel_type` only) — hydrogen, biomethane, liquid fuel, biomass, solar, wind, water

Required lookup precedence:
1. `fuel_price_node` (generator-specific)
2. `fuel_type` (global fallback)

Required regularization:
- Coal and gas prices are currently keyed by generator name in wide format. **All generators that currently have generator-specific fuel prices retain those prices using the `fuel_price_node` mapping.** The generator name becomes the `fuel_price_node` value, and each generator's price trajectory is melted from wide to long format. This avoids any lossy collapse — no generator-specific pricing is lost in the restructuring.
- Liquid fuel, biomass, biomethane, and hydrogen prices are currently single-row wide-format tables; melt each to long format and merge.
- Free-carrier rows (Solar, Wind, Water) with `price = 0.0` are optional; translator can default to zero if absent.

Note:
- If fuel prices are provided at the region level, duplicate them across all `node_id` values in that region so generator mapping can be performed by `node_id`.

### 6b. `costs_new_entrant_build.csv`

```text
technology                       year  cost
"OCGT (small GT)"                2028  1602880.7
"Large scale Solar PV"           2028  1680939.6
"Battery Storage (4hr storage)"  2028  1350000.0
```

Source table: `new_entrant_build_costs`

### 6c. `costs_connection.csv` (consolidated)

```text
node_id  technology          year  connection_cost  system_strength_cost
N1                           2028  118121.36        137000.0
N1                           2029  119198.99        137000.0
NNSW     "OCGT (small GT)"  2028  85544000.00      0.0
```

Source tables: `new_entrant_wind_and_solar_connection_costs`, `new_entrant_non_vre_connection_costs`

Adopted decision:
- VRE connection costs are already time-varying; output directly in long format.
- Non-VRE connection costs are currently static (no year dimension). The templater duplicates non-VRE costs across all model years present in the VRE data so that every row has a `year` value (see design rule G). This allows the translator to perform a uniform join without special-casing.

Implementation notes:
- If connection costs are provided at the region level, duplicate them across all `node_id` values in that region so generator mapping can be performed by `node_id`.
- Normalize non-VRE technology names to the canonical entrant technology set.
- Interpretation rules:
  - If `technology` is blank, treat the row as node-level VRE connection costs.
  - If `technology` is present, treat the row as node + technology (non-VRE) connection costs.
- **Battery connection costs:** The current templater embeds `connection_cost_$/mw` directly in the `new_entrant_batteries` table via fuzzy matching against the non-VRE connection cost table (`storage.py`). Under the new schema, battery connection costs are no longer embedded in the asset table — instead, batteries join to `costs_connection.csv` on `node_id + technology + year`, the same as non-VRE generators. This moves the connection cost lookup from the templater to the translator. The `costs_connection.csv` table must include battery technology rows (e.g. "Battery Storage (1hr storage)") so the translator can perform this join. The fuzzy matching currently used in the templater should be replaced by pre-normalizing technology names in `costs_connection.csv` to exact-match the canonical technology names in `assets_new_storage.csv`.

---

## 7. Emissions (`emissions_*`)

### 7a. `emissions_reduction.csv`

```text
fuel_type    name                      year  gpg_reduction
Hydrogen     "Kogan Gas"               2028  100.0
Hydrogen     "SA Hydrogen Turbine"     2028  75.0
Biomethane                             2028  100.0
Biomethane                             2029  100.0
```

Source tables: `gpg_emissions_reduction_h2`, `gpg_emissions_reduction_biomethane`

Semantics:
- `name` is nullable; blank means global for that fuel type.
- Hydrogen reductions are generator-specific (e.g. Kogan Gas and SA Hydrogen Turbine have different blending rates).
- Biomethane reductions are global (apply to all biomethane-blending generators).

Required regularization:
- `gpg_emissions_reduction_h2` is currently keyed by generator name in wide format; melt to long format
- `gpg_emissions_reduction_biomethane` is currently a single row in wide format; melt and merge

---

## 8. Generator Dynamic Properties (`reliability_*`)

### 8a. `reliability_seasonal_ratings.csv`

```text
name       duid  region_id  timeslice        year  rating
Bayswater  BW01  NSW        summer_peak      2028  630.0
Bayswater  BW01  NSW        summer_typical   2028  660.0
Bayswater  BW01  NSW        winter           2028  660.0
Eraring    ER01  NSW        summer_peak      2028  640.0
```

Source table: `seasonal_ratings`

Regularization:
- Current seasonal ratings include storage names (e.g. batteries, pumped hydro). Filter to generator-only ratings. Storage seasonal ratings are dropped (not currently used by the translator).

### 8b. `reliability_outage_forecasts.csv` (merged)

```text
fuel_type               outage_type  year  rate
"Brown Coal"            full         2028  7.75
"Brown Coal"            partial      2028  11.56
"Black Coal NSW"        full         2028  6.31
"CCGT + Steam Turbine"  partial      2028  7.21
```

Source tables: `full_outage_forecasts`, `partial_outage_forecasts`

---

## 9. Policy Tables (`policy_*`)

### 9a. `policy_targets.csv`

```text
policy_id    region_id  year  metric          value
power_aus    NEM        2029  share_pct       71.0
power_aus    NEM        2030  share_pct       82.0
nsw_eir_gen  NSW        2028  generation_mwh  5547000.0
cis_storage  NEM        2028  capacity_mw     1531.0
vret         VIC        2028  share_pct       40.0
qret         QLD        2029  share_pct       50.0
tret         TAS        2028  generation_mwh  3200000.0
```

Source tables: `renewable_share_targets`, `powering_australia_plan`, `renewable_generation_targets`, `technology_capacity_targets`

Notes:
- Merges four tables with different value semantics into a single table using `metric` as discriminator:
  - `share_pct` — renewable share as percentage (from `renewable_share_targets`, `powering_australia_plan`)
  - `generation_mwh` — renewable generation target in MWh (from `renewable_generation_targets`)
  - `capacity_mw` — technology capacity target in MW (from `technology_capacity_targets`)

### 9b. `policy_eligible_technologies.csv`

```text
policy_id        technology
cis_generator    "Large scale Solar PV"
cis_generator    Wind
cis_generator    "Wind - offshore (fixed)"
cis_storage      "Battery Storage (4hr storage)"
cis_storage      "Pumped Hydro"
nsw_generator    "Large scale Solar PV"
nsw_generator    Wind
nsw_storage      "Battery Storage (8hrs storage)"
vic_storage      "Battery Storage (1hr storage)"
vic_offshore_wind  "Wind - offshore (fixed)"
qret             Hydro
vret             "Large scale Solar PV"
tret             Wind
power_aus        "Solar Thermal (15hrs storage)"
```

Source table: `policy_generator_types` (manually extracted, ships with ISPyPSA in `manually_extracted_template_tables/`)

Changes:
- rename `generator` column to `technology`
- rename table from `policy_generator_types` to `policy_eligible_technologies`
- this is a manually extracted table; the CSV file in `manually_extracted_template_tables/{version}/` must be migrated to the new column naming

Regularization:
- align policy ID namespace between targets and eligibility tables (current inputs include `nsw_eir_*` in targets vs `nsw_*` in eligibility)

---

## 10. Custom Constraints (`constraints_*`)

### 10a. `constraints_rhs.csv`

```text
constraint_id   constraint_type  rhs
NQ2             <=               2500
SQ1             <=               1400
SWV1            <=               1850
MN1             <=               2000
"MN1 North"     <=               800
NSA1            <=               1125
"NSA1 North"    <=               350
NET1            <=               1600
SEVIC1          <=               6000
SWQLD1          <=               5300
S1-TBMO         <=               350
```

Source table: `custom_constraints_rhs` (manually extracted, ships with ISPyPSA in `manually_extracted_template_tables/`)

Notes:
- `constraint_id` values are constraint group IDs, distinct from both REZ IDs and node IDs
- The translator also auto-generates additional RHS entries for VRE build/resource limits (from `node_resource_limits`) and transmission expansion limits; these are not in the templater output

### 10b. `constraints_lhs.csv`

```text
constraint_id  term_type          term_id                          coefficient
NQ2            path_flow          CQ-NQ                            -1
NQ2            path_flow          Q4-CQ                            1
NQ2            path_flow          Q5-CQ                            1
SQ1            path_flow          SQ-CQ                            -0.5
SEVIC1         path_flow          V5-VIC                           1
SEVIC1         path_flow          TAS-VIC                          1
SEVIC1         generator_output   "Loy Yang A Power Station"       1
SEVIC1         generator_output   "Yallourn W"                     1
SWQLD1         generator_output   Tarong                           0.6
SWQLD1         generator_output   Borumba                          0.5
S1-TBMO        path_flow          SESA-CSA                         0.3
S1-TBMO        generator_output   "Tailem Bend Solar Farm"         1
S1-TBMO        storage_output     "Tailem Bend Battery Project"    1
```

Source table: `custom_constraints_lhs` (manually extracted, ships with ISPyPSA in `manually_extracted_template_tables/`)

Notes:
- `term_type` values and their PyPSA mapping:
  - `path_flow` -> Link component, attribute `p` (dispatch flow). Renamed from `link_flow` to align with the `path_id` terminology used in `network_transmission_paths.csv` and decouple the constraint schema from PyPSA implementation details.
  - `generator_output` -> Generator component, attribute `p` (dispatch output)
  - `generator_capacity` -> Generator component, attribute `p_nom` (installed capacity)
  - `storage_output` -> StorageUnit component, attribute `p` (dispatch output)
- `term_id` for `path_flow` uses `path_id` values from `network_transmission_paths.csv` (e.g. `CQ-NQ`, `TAS-VIC`)
- `term_id` for `generator_output` and `storage_output` uses asset `name` values from asset tables

Regularization:
- `link_flow` is renamed to `path_flow` in the manually extracted `custom_constraints_lhs.csv` file. The `term_id` values currently use sub-region pairs (e.g. `CQ-NQ`); after restructuring these become `path_id` values, which are the same strings — no change needed as long as `path_id` retains the `node_from-node_to` format.
- `generator_output` and `storage_output` `term_id` values reference asset names; ensure asset name consistency between constraint tables and asset tables.

### Constraint group expansion costs

Some `rez_transmission_expansion_costs` rows are keyed by constraint group IDs (e.g. `NQ2`, `SEVIC1`) rather than REZ IDs. These are retained in `network_transmission_path_expansion_costs.csv` with the constraint group ID as `path_id`. The translator uses these costs to create dummy expansion generators that relax constraint RHS values at a cost. This is existing translator logic and does not require templater changes beyond including these rows in the merged expansion costs table.
