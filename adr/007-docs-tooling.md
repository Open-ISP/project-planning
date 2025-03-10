
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
  - https://docusaurus.io/
  - Markdown based
  - self hosted, deploying on Github pages is supposed to be easy
  - need third party tool to pydoc-markdown to do docstrings
  - can do built in math equations with katex
  - can build and preview locally
  - versioning seems to be built in

- Dosify
  - https://docsify.js.org/#/
  - Markdown based
  - self hosted, deploying on Github pages is supposed to be easy
  - need third party tool to pydoc-markdown to do docstrings
  - can use plugin to do latex
  - can build and preview locally
  - can do versioning with plugin

- Hugo

## Outcome

I think Mkdocs is the option we should go with, because:

  - It appears to be one of the simpler to implement and use solutions
  - The functionality for automating the processing of docstrings looks better 
    intergrated than Docusaurus and Dosify
  - I don't want to go with a freemium model such as Mintlify or GitBooks where we
    may need to pay to get the functionality we need.
  - There don't appear to be any capability gaps between what we need and what Mkdocs 
    can do