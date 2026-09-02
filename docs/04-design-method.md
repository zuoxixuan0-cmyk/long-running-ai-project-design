# Design Method

A long-running AI project usually does not become reliable by designing
every mechanism in advance.

The design evolves through a cycle:

``` text
Observed problem
→ Isolate the behavior
→ Test the boundary
→ Make the smallest useful change
→ Observe again
```

This document describes the process I use to turn failures into design
decisions.

The goal is not to create the largest architecture.

The goal is to keep a traceable relationship between:

``` text
Problem

↓

Design Decision

↓

Implementation

↓

Observed Behavior
```

------------------------------------------------------------------------

## Start from failures, not features

A common mistake is starting with:

> What capabilities should this AI system have?

That often leads to adding mechanisms before knowing whether they solve
anything.

A better starting point is:

> What specific behavior is failing?

For example:

Too broad:

``` text
The AI needs better memory.
```

More useful:

``` text
A temporary hypothesis from a previous discussion
was treated as formal Current without adoption.
```

The second description identifies a boundary that can be tested.

------------------------------------------------------------------------

## Isolate one behavioral slice

Large AI projects contain many interacting behaviors.

When something fails, I try to reduce it to the smallest observable
unit.

A behavioral slice should answer:

-   What input condition creates the situation?
-   What behavior is expected?
-   What behavior actually happens?
-   Which boundary does this test?

Example:

``` text
Given:

Current = A


When:

The conversation discusses B,
but B is never adopted


Then:

A later invocation should still resolve Current as A.
```

This does not test the entire project.

It tests one responsibility boundary:

``` text
Conversation
≠
Current State
```

------------------------------------------------------------------------

## Use synthetic cases before complex scenarios

Synthetic cases remove unnecessary variables.

They are useful because a failure can be attributed more easily.

Example:

``` text
Session 1:

Current = A


Session 2:

Discuss B


Session 3:

Ask what is current
```

The purpose is not to simulate production perfectly.

The purpose is to answer:

> Does the mechanism behave coherently in a controlled situation?

Synthetic cases are especially useful for:

-   state authority;
-   persistence decisions;
-   recovery behavior;
-   rule boundaries.

------------------------------------------------------------------------

## Test where the system actually runs

A design can be logically consistent and still behave differently in a
real host.

The host may affect:

-   instruction following;
-   context handling;
-   tool behavior;
-   file operations;
-   execution limits.

Therefore:

``` text
Design correctness
≠
Observed host behavior
```

A mechanism should be tested in the environment where it will actually
operate.

------------------------------------------------------------------------

## Describe failures precisely

Broad descriptions are difficult to improve.

For example:

``` text
The model is unstable.
```

does not identify a repair path.

A better description:

``` text
The system loaded Current=A,
but later summarized recent discussion
and treated B as the new Current.
```

This points toward a specific authority problem.

The purpose of failure analysis is not to assign blame.

It is to identify which responsibility failed.

------------------------------------------------------------------------

## Make the smallest useful change

Once the failure is understood, avoid immediately adding a large
mechanism.

A common pattern is:

``` text
Failure
→ Add rule
→ Another failure
→ Add more rules
→ Increasing complexity
```

Instead:

``` text
Observed failure
→ Minimal change
→ Retest
```

For example:

If conversation can override Current, first strengthen the Current
boundary.

Do not immediately introduce:

-   additional memory layers;
-   new state systems;
-   complex recovery mechanisms.

Every new mechanism creates more interactions.

------------------------------------------------------------------------

## Retest the original failure

After a change, first verify that the original problem is solved.

The important question is:

> Did the failure disappear?

Not:

> What other improvements can be added?

This keeps development connected to evidence.

------------------------------------------------------------------------

## Test interactions, not only isolated rules

A rule can work correctly by itself and still create problems with
another rule.

Example:

Rule A:

``` text
Conversation should not casually replace Current.
```

Rule B:

``` text
New evidence may update Current.
```

Both are reasonable.

But if Rule A becomes too strong:

``` text
Current can never change.
```

the system creates a new failure.

Interaction testing checks whether valid mechanisms remain compatible.

------------------------------------------------------------------------

## Turn failures into regression cases

When a failure is fixed, keep a version of the case.

Example:

``` text
Repeated discussion of B
must not replace Current=A
without formal adoption.
```

Future changes can be checked against the same behavior.

The regression set should grow from observed problems, not from trying
to predict every possible problem.

------------------------------------------------------------------------

## Use ablation to remove unnecessary complexity

Design evolution should include removal.

Ablation asks:

> Does this mechanism still provide value?

A simple process:

``` text
Remove or weaken a rule

↓

Run relevant tests

↓

Observe whether behavior degrades
```

If nothing changes, the mechanism may be:

-   unnecessary;
-   duplicated;
-   already covered elsewhere.

A system that only accumulates rules becomes difficult to understand.

------------------------------------------------------------------------

## Use adversarial cases for important boundaries

Some failures only appear when the system is strongly pushed toward the
wrong behavior.

Examples:

### State authority

Normal:

``` text
Mention B once.
```

Adversarial:

``` text
Discuss B repeatedly
with much more recent context than A.
```

Question:

Does repetition create false authority?

------------------------------------------------------------------------

### Evidence independence

Normal:

``` text
One source supports C.
```

Adversarial:

``` text
Ten sources support C,
but all originate from one source.
```

Question:

Does quantity replace evidence quality?

------------------------------------------------------------------------

### Rule promotion

Normal:

``` text
One useful observation appears.
```

Adversarial:

``` text
Many similar observations appear.
```

Question:

Does the system turn patterns into rules without validation?

------------------------------------------------------------------------

## Keep validation claims precise

Different testing stages prove different things.

``` text
Designed

↓

Synthetic-tested

↓

Target-host tested

↓

Interaction-tested

↓

Regression-protected

↓

Production-observed
```

These stages should not be treated as equivalent.

A design document shows intent.

A synthetic test shows controlled behavior.

A host test shows environment behavior.

Production observation shows real usage behavior.

Each provides different evidence.

------------------------------------------------------------------------

## The design loop

The process can be summarized as:

``` mermaid
flowchart LR
    P[Observed Problem]
    B[Behavioral Slice]
    S[Synthetic Case]
    T[Target Host Test]
    M[Minimal Change]
    R[Retest]
    G[Regression]
    A[Ablation]

    P --> B --> S --> T --> M --> R --> G --> A
```

The purpose of this method is not to prove that an AI project is
finished.

It is to make sure every important piece of complexity has a reason to
exist.

If a rule cannot be connected to an observed problem, a design decision,
or evidence that it improves behavior, it should be reconsidered.
