
# Independent data store

## Status
Proposed and trying it out

## Decision

Self host AEMO trace data on independent data store (object store)

## Context
Discussed in context of managing downloading and unpacking the ~62 trace file zips from AEMO (a relatively cumbersome and manual task), currently required to use the ISP trace parser package.

## Options Considered

Helper functions within ISP trace parser package to download and manage the zips:

- Directly from AEMO:
    - Pros:
        - Points directly at the official data from AEMO.
    - Cons:
        - Potentially fragile (links and data may change).
        - Package pointing at AEMO links violates robots.txt
        - (AEMO Data may change / be updated)

- Hosting zips on object store
    - Pros:
        - Persistent archive of AEMO data and versioning (in event data changes)
        - Less fragile (can point to persistent links)
    
    - Cons:
        - Additional management of data and maintenance overhead
        - Potentially some ongoing (albeit small) costs

## Consequences
Easier to develop some more robust helper functions to download the relevant data (which in turn could make the package easier to use out of the box)

There more administrative and maintenance overhead (e.g. setting up object store and maintaining that) 