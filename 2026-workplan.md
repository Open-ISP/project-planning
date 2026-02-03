# 2026 Work plan summary

This document summarises the OpenISP work plan for 2026. The original draft is a synthesis of the early 2026
planning workshops held in Melbourne.

The general approach discussed was to:

- Postpone introducing backwards compatibility for old ISP versions and focus on updating to support the 2026.
- Implement data validation processes to support flexible inputs and improve software robustness.
- Then proceed incrementally adding modeling methodology features, based on priority order.*

*Note, this is an explicit shift away from the `MVP` framing discussed previously. Rather than trying to strictly
define an MVP to work towards (which is use case dependent), features will be sorted into a rough priority order and 
released incrementally as completed.

The key tasks and priorities are outlined in a series of tranches, with each tranche indicating a set of tasks with
roughly equal priority which can be implemented (mostly) in parallel.

## Tranche 1 - 2026 ISP update and data validation:

- Review translater output (ISPyPSA tables format)
- Update translater to work 2026 ISP IASR and implement changes flowing from review
- Implement data validation for ISPyPSA tables and translator tables
- Update the trace parser to work with 2026 ISP traces
- Update ISPyPSA to support PyPSA 1.0 and lastest Python 3.10-14

## Tranche 2+ (to be broken up with prioritisation):

- Generators
    - Hydro
        - using inflows and general structure of AEMO methods
    - Finalise utility storage
    - CER storage
    - Outages (deratings for capacity outlook)
    - Seasonal ratings
    - Renewable energy entrants outside REZs (if minimal work required)
    - Reserves
    - Hydrogen

- Network Network
    - Seasonal ratings
    - Network losses

- Policy constraints
    - Carbon budget
    - Renewable energy target
    - Storage targets_

- Temporal resolution reduction
    - sampled chronology
    - (alternative is to do representative year for an investment period??)

