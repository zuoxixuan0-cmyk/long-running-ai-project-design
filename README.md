# Long-Running AI Project Design

A set of public design notes about building AI projects that can
continue across separate invocations.

The starting point of this work was not a search for a better memory
system.

The recurring problem was different:

A long-running AI project needs to know what information represents its
current state, what information is only historical context, what can be
trusted, and what is allowed to change.

A conversation can contain useful information, but it is not
automatically the current state of a project.

A stored state can represent where a project stopped, but it does not
automatically prove that external reality has remained unchanged.

A previous conclusion can be useful, but it should not automatically
become a permanent rule.

These distinctions become increasingly important as AI projects continue
across many sessions.

------------------------------------------------------------------------

## Core idea

This repository explores a simple separation of responsibilities:

``` mermaid
flowchart TB
    H[Host AI]
    L[Working Logic]
    C[System Contract]
    S[Persistent State]

    H -->|executes| L
    S -->|provides formal state| L
    L -->|forms persistence intent| C
    C -->|governs persistence| S
```

### Host AI

The execution environment where the project runs.

This may include:

-   model behavior;
-   tools;
-   context handling;
-   file access;
-   interaction capabilities.

The host affects behavior, but it should not be the only place where
project continuity exists.

### Working Logic

The current operating behavior of the project.

It covers questions such as:

-   how requests are interpreted;
-   how information is evaluated;
-   how context is constructed;
-   how the project decides whether something has long-term value.

### System Contract

The stable rules that define persistent state.

It describes:

-   what kinds of state exist;
-   where authority comes from;
-   how updates are interpreted;
-   how recovery should work.

### Persistent State

The formal information that future invocations need in order to
continue.

It is not intended to be a complete archive of everything that happened.

------------------------------------------------------------------------

## Why this separation matters

Long-running AI projects often fail when different kinds of information
are treated as the same thing.

Examples:

``` text
Conversation
≠
Current State
```

``` text
Recovered State
≠
Current Reality
```

``` text
Learning
≠
Rule Change
```

The goal is not to store everything.

The goal is to preserve the information that future invocations actually
need while keeping authority and change boundaries clear.

------------------------------------------------------------------------

## Documentation

### Problem Model

Why these problems appear and what failure patterns motivated the
design.

→ [01 \| Problem Model](docs/01-problem-model.md)

### Architecture

The responsibility boundaries between host, logic, contract, and state.

→ [02 \| Architecture](docs/02-architecture.md)

### Principles

The design principles used to evaluate decisions.

→ [03 \| Principles](docs/03-principles.md)

### Design Method

How failures, tests, and design changes are handled.

→ [04 \| Design Method](docs/04-design-method.md)

### Worked Example

A synthetic walkthrough showing state changes, recovery, and persistence
decisions.

→ [05 \| Worked Example](docs/05-worked-example.md)

### Recovery After a Gap

Why recovering a previous state is different from validating current
reality.

→ [06 \| Recovery After a Gap](docs/06-recovery-after-a-gap.md)

### Testing Long-Running AI Behavior

A testing approach focused on boundaries, failures, and regression.

→ [07 \| Testing Long-Running AI
Behavior](docs/07-testing-long-running-ai-behavior.md)

------------------------------------------------------------------------

## Scope

This repository is a public design note collection.

It discusses:

-   problems;
-   architectural boundaries;
-   design decisions;
-   testing approaches;
-   abstract patterns.

It does not publish:

-   production contracts;
-   production prompts;
-   production schemas;
-   real persistent state;
-   business logic;
-   implementation-specific details.

This is not presented as a universal AI framework.

Some ideas are stable design patterns. Others are still being explored
across different project shapes and host environments.

This repository represents a public snapshot of the design process.
