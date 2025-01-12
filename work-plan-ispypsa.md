# ISPyPSA work plan

The purpose of this document is to outline the current milestones we are working towards for ISPyPSA and any 
intermediate goals. A brief summary of goals are provided, including their current status, who is carry out the work,
and any links to more detailed documentation, GitHub issues, or pull requests.

# Milestones:

## Version 1.0: Production cost and single stage long-term modelling capabilities

- Production cost model with all existing generators and fuel cost and demand data from a specified model year.
- Single stage long-term model with all new entrant options and network expansion.

### Generators 

- [ ] Existing (thermal and variable renewable)
  - [ ] Dynamic marginal costs (marginal cost change by model year)
  - [x] Wind and solar reference year traces used for availability
  - [ ] Full and partial outages

- [ ] New entrant (thermal and variable renewable)
  - [ ] Dynamic marginal costs (marginal cost change by model year)
  - [ ] Dynamic and locational build cost
  - [ ] Wind and solar traces used for availability
  - [ ] Full and partial outages 
  - [ ] REZ resource limits
  - [ ] Renewable energy entrants outside REZs??

- [ ] Existing Batteries

- [ ] New Entrant Batteries

- [ ] Hydro

### Network

- [ ] Existing network representation (down to subregional)
  - [ ] limits between subregions
  - [ ] rez export limits
    - [ ] add rez build limits table to the workbook table cache
    - [ ] add rez build limits table to ISPyPSA inputs (templater)
      - [ ] replace absent transmission limit with inf
    - [ ] add rez transmission connections to lines.csv in PyPSA friendly inputs
    - [ ] add hard coded table of cleaned rez group constraints to ISPyPSA
    - [ ] add rez group constraints to ISPyPSA inputs
    - [ ] add rez group constraints to a custom_constraints.csv in PyPSA friendly inputs
    - [ ] add create custom constraint functionality in model module

  - [ ] group constraints that link rez and subregion transfer limits
  
- [ ] Network expansion options and cost

- [ ] Network losses??
  - [ ] interregional loss equations
    Would need to be done with custom constraints 
  - [ ] Generator MLFs

### Demand

- [x] Reference year based traces

### Reserves??

To be further defined, see ISP Methodology section 2.4.3.

### Temporal resolution reduction

- [x] Weeks subset by week number
- [ ] Representative weeks??
- [ ] Sampled chronology??
  Used in AEMO Single Stage Long-term model
- [ ] Fitted chronology??
  Used in AEMO Detailed Long-term model

### Spatial resolution reduction

- [ ] Subregional
  - [ ] as per AEMO method with rez nodes attached to subregions
  - [ ] option to have rez generators connected directly to subregion nodes
  
- [ ] Regional
  - [ ] templater chooses network subregional connection cross region boundary to 
    represent interregional connection
  - [ ] rezs as separate nodes connected to regions as distinct nodes 
  - [ ] option to have rez generators connected directly to region nodes
  - [ ] choose just once subregion/rez to use build cost from per region
  
- [ ] Single region
  - [ ] rezs as separate nodes connected to region as distinct nodes 
  - [ ] option to have rez generators connected directly to single region
  - [ ] choose just once subregion/rez to use build cost from per region

- [x] demand aggregated

### Plotting

- [ ] Basic set of plots produced by default when model is run

### Documentation

What are the basic set of docs that are needed for version 1.0?

### Testing

- [ ] Unit testing
- [ ] Model comparison against ISP