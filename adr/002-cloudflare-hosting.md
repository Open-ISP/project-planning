
# Cloudflare hosting

## Status
Proposed and trying it out

## Decision

Use cloudflare R2 object store for storing and accessing data

## Context
Setting up persistent data store (as per [ADR 001](001-independent-data-store.md))

## Options Considered

Originally considered using linode object store (pointing `data.openisp.au` subdomain directly to the bucket), with possibility of using cloudflare as CDN (including for providing SSL etc)

- Pros: 
    - Linode is somewhat simple, and has a reasonably price allowance (250GB, $5/mo), likely enough for our purposes
    - (The smaller parquet files could be cached by CDN - if we end up hosting them)

- Cons:
    - Egress charges _could_ end up becoming large (if usage becomes significant)
    - Only files less than 100MB cached via cloudflare (so no real benefit of CDN for the zip files)
    - Complexity managing multiple services and interaction between them. 
    - Base cost is likely more

Alternative considered was using Cloudflare R2:

- Pros: 
    - No egress costs at all
    - Simplicity of object store and DNS settings within single service

- Cons:
    - Only 10GB free (would like need to pay a small monthly amount for our needs - less than linode). Has potential to become significant (if say more trace data uploaded over time) - though that might also be true of linode
    - More 

## Consequences
 - Using a new service (with some cost) - as both CDN, and object store
    - Point cloudflare DNS to R2 (rather than linode)
 - Need to use cloudflare api keys for uploading / storing data on R2 (some minor differences to s3api calls)
 - Linode bucket deprecated (and will be deleted)
 - Shouldn't notice any differences to data requested from `data.openisp.au` (not that anyone is actually doing that now anyway)