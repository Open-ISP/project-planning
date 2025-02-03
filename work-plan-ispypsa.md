# ISPyPSA work plan

The purpose of this document is to outline the current milestones we are working towards for ISPyPSA and any 
intermediate goals. A brief summary of goals are provided, including their current status, who is carry out the work,
and any links to more detailed documentation, GitHub issues, or pull requests.

# Milestones:

## Initial release v0.X.0: Production cost and single stage long-term modelling capabilities

- Production cost model with all existing generators and fuel cost and demand data from a specified model year.
- Single stage long-term model with all new entrant options and network expansion.

### Generators 

- [ ] Existing (thermal and variable renewable)
  - [ ] Dynamic marginal costs (marginal cost change by model year)
  - [x] Wind and solar reference year traces used for availability

- [ ] New entrant (thermal and variable renewable)
  - [ ] Dynamic marginal costs (marginal cost change by model year)
  - [ ] Dynamic and locational build cost
  - [ ] Wind and solar traces used for availability
  - [ ] REZ resource limits

- [ ] Existing Batteries

- [ ] New Entrant Batteries

- [ ] Hydro
  - [ ] Hydro data and material on AEMO approach (Dylan to follow up)
  - (note: potential approach of using generic storage object with a link for inflow (energy) from hydro data)

### Network

- [ ] Existing network representation
  - [ ] limits between subregions
  - [ ] rez export limits
  - [ ] group constraints that link rez and subregion transfer limits

- [ ] Network expansion options and cost

### Demand

- [x] Reference year based traces

### Temporal resolution reduction

- [x] Weeks subset by week number
- [ ] Representative weeks

### Spatial resolution reduction

- [ ] Subregional
  - [ ] as per AEMO method with rez nodes attached to subregions

- [ ] Ability to run model with subset of regions
  Main reason to allow faster run times.

### Long-term to short-term model interface

- [ ] Long-term model results can be exported to ISPyPSA input format for use in 
  production cost modelling

### Plotting

- [ ] Basic set of plots produced by default when model is run

### Documentation

- [ ] Introduction
- [ ] High-level contextualising documentation 
- [ ] Methodology documentation
- [ ] Workflow documentation
- [ ] Getting started documentation
- [ ] API documentation

### Testing

- [ ] Unit testing

## Version 1.0.0: Production cost and single stage long-term modelling capabilities

Additional features for v1.0.0 building on the initial release

### Generators 

- [ ] Full and partial outages
- [ ] Unit commitment 
  - [ ] Minimum generation levels (I think related to unit commitment)
- [ ] Seasonal ratings
- [ ] Renewable energy entrants outside REZs

### Network

- [ ] Seasonal ratings 
- [ ] Network losses
  - [ ] interregional loss equations
    Would need to be done with custom constraints 
- [ ] Forward and reverse limits

### Demand

- [x] Reference year based traces

### Reserves??

To be further defined, see ISP Methodology section 2.4.3.

### Policy constraints

Integration of major policy constraints. Likely implemented through custom constraints. Including but not limited to:
- [ ] Global carbon budget constraint(s)
- [ ] Renewable Energy Target(s)
  - [ ] Including-state based targets 

### Temporal resolution reduction

- [ ] Sampled chronology??
  Used in AEMO Single Stage Long-term model
- [ ] Fitted chronology??
  Used in AEMO Detailed Long-term model

### Spatial resolution reduction

- [ ] Subregional
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

### Plotting

### Documentation

- [ ] Updated from initial release

### Testing

- [ ] Model comparison against ISP

#  Benchmarking / monitoring

Not tied to particular release schedule, some monitoring and reporting of model performance will be valuable as we develop / introduce greater complexity. 