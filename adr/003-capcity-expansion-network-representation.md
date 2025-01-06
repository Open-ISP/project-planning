
# Capacity expansion network representation

## Status
Under consideration

## Decision
This ADR is for the set of decision concerning how the transmission network is represent in the capacity outlook 
modelling capability.

Key decisions:

- Are PyPSA links or lines used?
- How are winter, summer typical, and summer peaking ratings implemented?
- Can we combine the Southern and Northern CNSW–SNW connections?
- Are we modelling losses?
- Are we implementing group constraints to represent subregional limits that affect multiple REZs?
- Are we including inter subregional flows in REZ transmission limits?
- Which transmission option to choose in the templater?
- Are we modelling transmission expansion from REZs to emerging load centres?

## Context

### Links and Lines

### Ratings

- AEMO uses winter rating for all winter periods.
- Summer peak rating for the subset of the hottest summer days.
- Average of summer typical and winter rating for all other days using the same approach as the ESOO. ESOO 
  methodology say this is the top five hottest days in the reference year. "the rating also applies to any additional 
  days with daily maximum temperatures that are within two degrees Celsius of the summer 10% POE demand reference 
  temperature." Although this from generator rather than network section of ESOO doc.
- "Seasonal definitions reflect those specified in the 2022 ESOO; that is, summer ratings are applied between November 
  to March and winter ratings between April to October"

Outstanding questions:

- Where can we get the reference year temperature data?

### CNSW–SNW implementation

Need to clarify why this is implemented as two separate connections in the ISP.

### Losses

- ISP models losses on notional interconnectors between regional reference nodes using a piecewise linear 
  representation.

### Group constraints

Sometimes the exports of mutiple REZs in the same subregion are limit by the same network limitation. In these cases
group constraints are applied that place a limit on the combined exports from multiple REZs.

### Inter subregional flows impact on REZ export limit

Inter-subregional connections sometimes pass through REZs which can cause the inter-subregional flows to impact the 
ability of the REZ to export power. AEMO, in these cases, therefore, include the flow on the lhs of the equations
which limit REZ exports.

### Choosing initial augmentation option

- ISP says typically the least cost option is chosen initially: "This point will generally be the
  least-cost linearised value as a starting point (for example, Option 1). If the optimised model builds significantly
  more or less generation in the REZ compared to the chosen point, then the point can be revised"

### Emerging load centres 

These are for hydrogen electrolysers at ports.

## Options Considered

## Consequences