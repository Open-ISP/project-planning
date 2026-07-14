# Workbook 7.8 content changes (vs 7.5)

This note just includes some notable changes to *content*  (rather than table definitions, schema's and so on) that were noticed when updating the table configs.  These are change that might be worth knowing about for downstream use in `ISPyPSA`, or in `isp-trace-parser` (and almost certainly will not be complete).

---
## Generator changes

- **85 new projects** (see [`new_projects_7.8.md`](https://github.com/Open-ISP/project-planning/blob/main/ISP2026/new_projects_7.8.md)), for the large part batteries
- **2 cancelled** (Ravenswood BESS, 20 MW anticipated; Riverina Solar Farm, 32.4 MW
  *committed*)
- **2 renamed / split** (QEJP - Borumba → Borumba Pumped Hydro, Goyder North Wind
  Farm split into Goyder North Wind Farm 1 & 2)
	- Borumba rename incomplete?  Some tables still seem to use / refer to old name and IASR ID.

 **Tables affected:**
- Summary mapping (existing generators)
- Gas and liquid fuel prices 
	- (Dubbo GT added across 3x scenarios, existing and secondary fuel tables)
- Generator attribute tables all get new entries:
	- (auxiliary, emissions intensity, fixed/variable OPEX, heat rates, max capacity, closure years, seasonal ratings, ramp rates, GPG MSL, affine heat rates, capacity factors, connection costs/forecasts, MLFs)

---
## Zone changes

 - **N9 REZ (Hunter-Central Coast) split into N9a/N9b**:
	 - generation options  duplicated / in both halves
	 - Transmission limits / constraints generally split
 - **REZ location renames:** 
	 - Q2 North Qld Clean Energy Hub →  Hughenden Hub; 
	 - V3 Wimmera Grampians and V4 Wimmera Southern Mallee both named "Western Victoria" (somewhat confusing have different IDs but same name).
		 - This is apparently due to alignment with Victorian Transmission Plan
			 - (footnote 4	*The candidate REZs in Victoria have been updated to be consistent with the 2025 Victorian Transmission Plan released in August 2025. In May 2026, the Minister for Energy and Resources formally declared five REZs in regional Victoria . These declared REZs differ slightly from those identified in the 2025 Victorian Transmission Plan, which formed the basis of the 2026 ISP modelling.The candidate Victorian REZs V3 and V4 are part of the 2025 Victorian Transmission Plan "Western REZ".* )

**Tables effected**:
- Summary mapping (new entrants)	
- Electrolysers (capacity split between N9a and N9b)
- New entrant  attribute tables all get new entries (for N9a and N9b) and new names
- Locational Cost factors: - N9 split into N9a/N9b  (both halves have N9's factor values identically).
- Some REZ augmentation options allocated to specific halves of N9
	- (N9a gets Option 2b;   N9b gets Options 1, 2a, 3, 4)
	- Or in case of Vic, reallocated. 

---
## Granularity changes

In several places there are changes to granularity (e.g. NEM region to ISP-subregion)

**Tables affected:**
- Gas and liquid fuel prices  - new entrant gas from NEM region to sub-regions:
	-  5 NEM regions  × CCGT/OCGT (10 per scenario) → 15 ISP sub-regions × OCGT/CCGT (30 per scenario).
- Energy efficiency
	- State → sub-region granularity in all forecast tables (5 → 15 rows) - with a both region  and subregion columns

---

## Hybrid site limits (new sheet in 7.8)

New sheet and table: charging/dispatch limits for hybrid sites
- VRE + battery behind one connection point)
- One table:  58 rows = 28 sites (mostly solar + BESS pairs);
	- One site (Bundey) has 1 solar + 3 BESS),
- Columns:  IASR ID / Status / Technology / Region / Site Name (merged per site) / Connection
  Capacity (MW). Regions: VIC 20, NSW 20, QLD 12, SA 6 rows.

---
## Other changes

- **Gas and liquid fuel price sheet**:  Consultant scenario mapping table deleted
- **Energy efficiency:
	- "Reduced Energy Efficiency" **renamed** "Lower Energy Efficiency"
	- **New** "Higher Energy   Efficiency"  scenario
- **Hydrogen / Electrolysers:** 
	- Appears a bunch of duplicated entries in 7.5 have been removed in some tables 
		- (electrolyser MLF and hydrogen pipeline build cost tables were not de-duplicated)
	- New / changed naming convention for hydrogen piping
		- Instead of `REZ`  to `subregion` (e.g. "S1-NSA"), now uses "S1-S1"
			- However the IASR ID hasn't changed - at least in all places? (Still used S1-NSA etc) - see `summary mapping` sheet
	- electrolyser capacity split between N9a and N9b
- **Distribution network** sheet - New "connection pipeline to 2029-30" columns added (but noting in them)
- **Gas network and system**  Handful of renames (and Kurri Kurri pipeline added)
	- Gunnedah basis also added in cost Reserves and resources and gas production costs
	- Nine of the Vic gas network entries seem to be consolidated in to one (in pipeline tariff sheet)
- **Energy Policy Targets sheet:**
	- NSW EIR targets expanded from single values to yearly trajectories, (and a new "NSW IIO Modelled … Target" row alongside the legislated EIR row)
	- NSW REZ maximum connection limits split by technology (was a single limit per REZ)
	  - **New policy tables:** 
		- NSW Roadmap Tender 7 (SNW Firming)
		- SA Firm Energy Reliability Mechanism


---

## Transmission augmentation changes

- Handful of new options and costs
- Looks like Victorian REZ constraint options renamed 

(Note: this is very incomplete / ad hoc)
#### New flow augmentations:
- **CQ-NQ**: Options 1 and 2 removed (were ~$1.82b and ~$5.33b at 2024-25)
- **SQ-CQ**: Option 7 added (~$62m at 2024-25)
- **CNSW-NNSW**: Options 1 and 2 removed (were ~$2.55b and ~$2.21b)
- **TAS-SEV**: Option 1 replaced by Option 2A.

## Vic constrain options
- **WV2** - looks to house the V3/V4 REZ group
- **NW1** - rename / re-allocation of V1 constraints

