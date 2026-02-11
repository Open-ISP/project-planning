# ISPyPSA Input Tables: Restructuring Plan

## Purpose
This document proposes a revised input-table structure and table relationships, plus the data regularization needed to support that structure.

Primary goals:
- Improve conciseness, readability, and intuitiveness.
- Reduce schema noise from unit-heavy and mapping-heavy columns.
- Shift time-varying facts from wide format (many year columns) to long format (many rows).

The covered input set is consolidated from 28 tables to 22 tables.

---

## Cross-cutting design rules

### A. Long format for time-series facts
Use `year` (integer) instead of year-per-column layouts.

### B. Remove units from column names
Use plain names such as `cost`, `price`, `capacity`, `fom`, and `vom`.
Document units in schema docs/metadata.

### C. Canonical naming for columns
Use consistent entity naming:
- `name` for asset names
- `technology` for technology labels
- `subregion_id`, `region_id`, `rez_id` for location keys

Canonicalize values (techs etc) across tables to remove dedicated mapping columns.

### D. Canonical naming for tables (family prefixes)
Use consistent table-family prefixes:
- `network_*` for topology and interconnector tables
- `rez_*` for REZ tables
- `assets_existing_*` and `assets_new_*` for unit definition tables
- `costs_*` for build, fuel, connection, and expansion costs
- `reliability_*` for dynamic reliability-related technical profiles
- `policy_*` for policy targets and eligibility

---

## 1. Network Topology (`network_*`)

### 1a. `network_subregions.csv`

```text
  subregion_id region_id
0         NNSW       NSW
1         CNSW       NSW
2           SQ       QLD
```

### 1b. `network_flow_paths.csv`

```text
   flow_path node_from node_to carrier
0  CNSW-NNSW      CNSW    NNSW      AC
1    NNSW-SQ      NNSW      SQ      AC
```

### 1c. `network_flow_path_limits.csv` (new)

```text
   flow_path direction       timeslice  capacity
0  CNSW-NNSW   forward  summer_typical       910
1  CNSW-NNSW   reverse  summer_typical       930
2    NNSW-SQ   forward  summer_typical      1078
3    NNSW-SQ   reverse  summer_typical       600
```

### 1d. `network_flow_path_expansion_options.csv` (new)

```text
   flow_path        option  additional_capacity
0  CNSW-NNSW      Option 2                  300
1    NNSW-SQ      Option 5                 1000
```

`option` is a provenance field for traceability. It can be dropped later if traceability is handled elsewhere.

### 1e. `network_flow_path_expansion_costs.csv`

```text
   flow_path  year        cost
0  CNSW-NNSW  2028  4975164.94
1  CNSW-NNSW  2029  4994562.23
2    NNSW-SQ  2028  2810000.00
3    NNSW-SQ  2029  2860000.00
```

---

## 2. Renewable Energy Zones (`rez_*`)

### 2a. `rezs.csv`

```text
  rez_id subregion_id carrier  resource_limit_penalty
0     N1         NNSW      AC                288711.0
1     N2         NNSW      AC                288711.0
2     Q1           SQ      AC                275000.0
```

### 2b. `rez_resource_limits.csv` (new)

```text
  rez_id resource_type  limit_type     limit
0     N1         solar  generation    6385.0
1     N1          wind    land_use    4755.0
2     N2     wind_high  generation    1800.0
3     N2   wind_medium  generation    5600.0
```

### 2c. `rez_network_limits.csv` (new)

```text
  rez_id       timeslice     limit
0     N1  summer_typical     171.0
1     N2  summer_typical     577.0
2     Q1  summer_typical     420.0
```

### 2d. `rez_transmission_expansion_options.csv` (new)

```text
  rez_constraint_id rez_id        option  additional_capacity
0                N1     N1      Option 1                  166
1              SWV1   SWV1     Option 1A                  500
```

### 2e. `rez_transmission_expansion_costs.csv`

```text
  rez_constraint_id  year         cost
0                N1  2028  28214281.29
1                N1  2029  28334436.97
2              SWV1  2028   8450000.00
3              SWV1  2029   8525000.00
```

---

## 3. Existing Units (`assets_existing_*`)

### 3a. `assets_existing_generators.csv`

```text
            name          technology region_id subregion_id rez_id   fuel_type fuel_price_node   fom  vom  heat_rate  capacity commissioning_date  closure_year  minimum_load
0      Bayswater  Steam Sub Critical       NSW         CNSW         Black Coal       Bayswater  64.5  5.1       9.45      2715         1985-01-01          2033           250
1        Eraring  Steam Sub Critical       NSW         CNSW         Black Coal         Eraring  70.2  4.8       9.65      2880         1984-01-01          2032           260
2  Darling Downs                CCGT       QLD           SQ               Gas    Darling Downs  18.0  2.5       7.20       630         2010-07-01          2050           120
```

Changes:
- `generator -> name`
- `technology_type -> technology`
- `fuel_cost_mapping -> fuel_price_node`
- drop `status`
- unitless column names

### 3b. `assets_existing_storage.csv`

```text
                 name       technology region_id subregion_id rez_id fuel_type    fom  capacity  storage_hours commissioning_date  closure_year  lifetime  charging_efficiency  discharging_efficiency
0      Wallgrove BESS  Battery Storage       NSW          SNW         Battery  30.69        50              2         2023-07-01          2043        20                 91.1                    91.1
1  Wandoan South BESS  Battery Storage       QLD           SQ         Battery  31.20       100              4         2026-07-01          2046        20                 90.5                    90.5
```

Changes:
- drop `status`
- unitless column names

Implementation note:
- fix templater mapping issue where `lifetime` can be non-numeric.

---

## 4. New Entrant Units (`assets_new_*`)

### 4a. `assets_new_generators.csv`

```text
                      name               technology   resource_type region_id subregion_id rez_id fuel_type    fom    vom  heat_rate  lcf  lifetime  minimum_stable_level
0       ocgt_small_gt_nnsw          OCGT (small GT)                       NSW        NNSW               Gas  13.47  12.83      10.19  100        40                   0.0
1  large_scale_solar_pv_n1     Large scale Solar PV          solar        NSW        NNSW      N1     Solar  18.18   0.00       0.00  101        30                   0.0
2  wind_offshore_fixed_n10  Wind - offshore (fixed) offshore_fixed        NSW        CNSW     N10      Wind  18.18   0.00       0.00  100        30                   0.0
```

- Keep columns: `name, technology, resource_type, region_id, subregion_id, rez_id, fuel_type, fom, vom, heat_rate, lcf, lifetime, minimum_stable_level`.
- Drop columns: `status, build_limit_*, fuel_cost_mapping, connection_cost_technology, connection_cost_rez/_region_id, minimum_load_mw`.
- Joins:
  - build costs: `technology + year`
  - connection costs: `rez_id + year` (VRE scope) or `technology + region_id + year` (non-VRE scope)
  - fuel prices: precedence = node-specific, then `fuel_type + technology + region_id`, then `fuel_type`
  - REZ limits: `rez_id + resource_type` (resource), `rez_id + timeslice` (network)
- Required regularization:
  - canonicalize `technology` labels across dependent tables
  - canonicalize `resource_type` using snake_case across all tables
  - normalize VRE connection locations to `rez_id`
  - expand static non-VRE connection costs by carry-forward to all model years
  - treat `N0`/`V0` as explicit valid non-REZ cases
- Rationale for dropped columns:
  - legacy mapping columns are derivable from structural keys
  - build limits come from normalized REZ limit tables
  - lower bound is represented by `minimum_stable_level` (`p_min_pu`), so `minimum_load_mw` is unnecessary for new entrants

### 4b. `assets_new_storage.csv`

```text
                      name                     technology region_id subregion_id rez_id fuel_type   fom  storage_hours  lifetime  lcf  charging_efficiency  discharging_efficiency
0  battery_storage_1h_nnsw  Battery Storage (1hr storage)       NSW        NNSW           Battery  18.0              1        20  100                 91.1                    91.1
1    battery_storage_4h_sq  Battery Storage (4hr storage)       QLD          SQ           Battery  19.2              4        20  100                 90.8                    90.8
```

Changes:
- drop `status`
- drop `round_trip_efficiency`
- use `technology` as the technology descriptor

Relationship mapping notes:
- Build costs join on `technology + year` to `costs_new_entrant_build.csv`.
- Connection costs join on `scope=region_tech_non_vre` with `technology + region_id + year`.
- No battery-specific connection mapping columns are needed in the battery table.

---

## 5. Cost and Price Tables (`costs_*`)

### 5a. `costs_fuel_prices.csv` (consolidated)

```text
    fuel_type       technology region_id fuel_price_node  year   price
0  Black Coal                                   Bayswater  2028   1.98
1  Black Coal                                     Eraring  2028   9.69
2         Gas  OCGT (small GT)       NSW                   2028  10.50
3         Gas             CCGT       NSW                   2028  10.30
4    Hydrogen                                              2028  42.90
5       Solar                                              2028   0.00
```

Supported row scopes:
- node-specific (`fuel_price_node` set)
- technology-region-specific (`technology` + `region_id` set)
- fuel-global (`fuel_type` only)

Required lookup precedence:
1. `fuel_price_node`
2. `fuel_type + technology + region_id`
3. `fuel_type`

### 5b. `emissions_reduction.csv` (merged)

```text
    fuel_type                 name  year  gpg_reduction
0    Hydrogen            Kogan Gas  2028          100.0
1    Hydrogen  SA Hydrogen Turbine  2028           75.0
2  Biomethane                        2028         100.0
3  Biomethane                        2029         100.0
```

Semantics:
- `name` is nullable; blank means global for that fuel type.

### 5c. `costs_new_entrant_build.csv`

```text
                      technology  year       cost
0                OCGT (small GT)  2028  1602880.7
1           Large scale Solar PV  2028  1680939.6
2  Battery Storage (4hr storage)  2028  1350000.0
```

### 5d. `costs_connection.csv` (consolidated)

```text
                 scope rez_id       technology region_id  year  connection_cost  system_strength_cost
0              rez_vre     N1                        NSW  2028        118121.36              137000.0
1              rez_vre     N1                        NSW  2029        119198.99              137000.0
2  region_tech_non_vre         OCGT (small GT)       NSW  2028      85544000.00                   0.0
3  region_tech_non_vre         OCGT (small GT)       NSW  2029      85544000.00                   0.0
```

Scopes:
- `rez_vre` requires `rez_id`, `year`
- `region_tech_non_vre` requires `region_id`, `technology`, `year`

Adopted decision:
- regularize to time-varying costs for all scopes
- for non-VRE rows that are currently static, expand to one row per model year using carry-forward values
- use one translator path keyed by `scope` + location/technology + `year`

Implementation notes:
- templater must expand static non-VRE costs across all model years via carry-forward
- translator joins should be scope-aware and year-aware
- current VRE connection inputs mix REZ IDs and full REZ names; normalize all VRE location values to `rez_id` before consolidation
- normalize non-VRE technology names to the canonical entrant technology set

---

## 6. Generator Dynamic Properties (`reliability_*`)

### 6a. `reliability_seasonal_ratings.csv`

```text
   name       duid  region_id       timeslice  year  rating
0  Bayswater  BW01        NSW     summer_peak  2028   630.0
1  Bayswater  BW01        NSW  summer_typical  2028   660.0
2  Bayswater  BW01        NSW          winter  2028   660.0
3    Eraring  ER01        NSW     summer_peak  2028   640.0
```

Regularization:
- current seasonal ratings include storage names; split by component or apply explicit generator-only filtering before generator rating joins

### 6b. `reliability_outage_forecasts.csv` (merged)

```text
              fuel_type outage_type  year   rate
0            Brown Coal        full  2028   7.75
1            Brown Coal     partial  2028  11.56
2        Black Coal NSW        full  2028   6.31
3  CCGT + Steam Turbine     partial  2028   7.21
```

---

## 7. Policy Tables (`policy_*`)

### 7a. `policy_targets.csv`

```text
     policy_id region_id  year          metric      value
0    power_aus       NEM  2029       share_pct       71.0
1    power_aus       NEM  2030       share_pct       82.0
2  nsw_eir_gen       NSW  2028  generation_mwh  5547000.0
3  cis_storage       NEM  2028     capacity_mw     1531.0
```

### 7b. `policy_eligible_technologies.csv`

```text
       policy_id                     technology
0  cis_generator           Large scale Solar PV
1  cis_generator                           Wind
2  cis_generator        Wind - offshore (fixed)
3    cis_storage  Battery Storage (4hr storage)
4    cis_storage                   Pumped Hydro
```

Regularization:
- align policy ID namespace between targets and eligibility tables (current inputs include `nsw_eir_*` in targets vs `nsw_*` in eligibility)
