
# Docs tooling

## Status
Proposed

## Decision
We need to choose which tool/s to use for writing/compiling/hosting documentation.

## Context
A key goal of Open-ISP is to ensure the tools are well documented and therefore easy for users to adopt. To help the team write quality documentation we need to choose tool to
write/compile/host documentation that will be easy to use and have sufficient features.

## Options Considered

- Mintlify
    - https://mintlify.com/docs/quickstart
    - Markdown based
    - third party hosting
    - auto generates api references
    - custom domain hosting (free)
    - free teir with single editor (but may not matter if we are editing through GitHub)
    - Can write maths forumlas using latex code
    - simple local build `npm i -g mintlify`
    - appears to support versioning (i.e. users could look back at docs for older versions)

- GitBooks
    - https://www.gitbook.com/
    - Markdown based
    - third party hosting 
    - unclear how to auto generate api docs
    - custom domain hosting (paid)
    - free teir with single editor (but may not matter if we are editing through GitHub)
    - Can write maths forumlas using latex code
    - Hard to find docs on local building, seem more geared to online editing and publishing through a UI
    - appears to support versioning (i.e. users could look back at docs for older versions)

- Mkdocs
    - https://www.mkdocs.org/
    - Markdown based
    - self hosted, deploying on Github pages is supposed to be easy
    - can auto generate api docs with plugin
    - I think you can setup a custom domain with github pages
    - Can use latex via Material and MathJax or KaTex
    - build with and preview locally `mkdocs serve`
    - can use a tool like `mike` to handle versioning: https://github.com/jimporter/mike

- Docusaurus

- Dosify

- Hugo

## Consequences
What becomes easier or more difficult to do because of this change?