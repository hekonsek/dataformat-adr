# Data format: ADR (Architecture Decision Record) project

This data format describes how to define ADR (Architecture Decision Record)
collection projects.

An ADR project is a repository whose primary content is a versioned collection of
architecture decisions. The project can be consumed by people directly from
Markdown files and by tools such as [Spex](https://github.com/hekonsek/spex).

## Project layout

An ADR project should use this layout:

```text
📁 .
├── 📄 README.md
├── 📁 .spex/
│   └── 📄 spex.yml
├── 📁 spex/
│   └── 📁 adr/
│       ├── 📄 01-use-typescript-instead-of-javascript.md
│       ├── 📄 02-use-options-object-for-optional-parameters.md
│       └── 📁 security/
│           └── 📄 security_01-use-execfile-instead-of-exec.md
└── 📁 .github/
    └── 📁 workflows/
        └── 📄 ci.yml
```

Required files and directories:

- `README.md`: explains the purpose and scope of the ADR collection.
- `spex/adr/`: contains ADR Markdown files.

If you plan to export ADR project as Spex module for other projects:

- `.spex/spex.yml`: declares imported Spex packages.

Optional files:

- `spex/adr/<category>/`: groups ADRs by category, for example `security`.
- `.github/workflows/ci.yml`: validates the Spex project.
- `LICENSE`: declares repository license.

## README

The README should identify the collection and its target domain.

Example:

```md
# ADR (Architecture Decision Record) collection for Node CLI

This is ADR (Architecture Decision Record) collection containing Golden Path
recommendations for working with Node CLI.
```

## Spex configuration

The ADR project should be a Spex package. The minimal `.spex/spex.yml` file is:

```yml
packages:
  - hekonsek/syllaibus
```

CI should run `spex validate` to verify the project.

## ADR file location

All ADR files should live under `spex/adr`.

General ADRs should be direct children of `spex/adr`:

```text
spex/adr/01-use-typescript-instead-of-javascript.md
spex/adr/02-use-options-object-for-optional-parameters.md
```

Category-specific ADRs may live in subdirectories:

```text
spex/adr/security/security_01-use-execfile-instead-of-exec-and-shell-enabled-spawn-to-prevent-shell-injection.md
```

## ADR file names

General ADR files should use this naming convention:

```text
NN-slugified-markdown-heading.md
```

Rules:

- `NN` is a zero-padded sequence number.
- The sequence number width should be the least possible width that still allows
  files in the directory to sort alphanumerically.
- Sequence numbers are monotonic within the directory.
- The title slug is derived from the ADR Markdown `#` heading.
- The title slug is lowercase kebab case.
- The file extension is `.md`.

For example, a directory expected to contain up to 99 ADRs should use `01`,
`02`, ..., `99`. A directory expected to contain up to 999 ADRs should use
`001`, `002`, ..., `999`.

The filename slug should match the ADR heading:

```text
📄 01-use-typescript-instead-of-javascript.md
```

```md
# Use TypeScript Instead of JavaScript
```

Category-specific ADR files may include a category prefix:

```text
category_NN-slugified-markdown-heading.md
```

Example:

```text
security_01-use-execfile-instead-of-exec.md
```

## ADR document format

Each ADR should be a Markdown document with one top-level heading and these
sections:

```md
# Use TypeScript Instead of JavaScript

## Context

Describe the situation, constraints, forces, and problem being solved.

## Decision

State the selected decision clearly.

## Consequences

Describe positive and negative consequences of the decision.

## Alternatives Considered

Describe the relevant alternatives and why they were not selected.
```


## Writing style

Spex ADR style should be concise, direct, and straight to the point.

ADR text should be practical and prescriptive:

- Explain the project context before stating the decision.
- Use `We will ...` for accepted decisions.
- Keep consequences balanced by documenting positive and negative trade-offs.
- Prefer concrete examples when the decision affects source layout, command
  usage, dependencies, or security posture.
- Keep the text short and focused on the decision.
- Avoid implementation details that do not affect the decision.

## Validation

The CI workflow should install Spex and run validation:

```yml
name: CI

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  CI:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Install spex
        run: npm install -g @hekonsek/spex

      - name: Spex validate
        run: spex validate
```
