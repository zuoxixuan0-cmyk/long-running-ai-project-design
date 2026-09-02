# Testing Long-Running AI Behavior

A long-running AI project is difficult to evaluate with ordinary
software-style tests.

The reason is not that testing is impossible.

The reason is that many failures are not about whether a function
returns the expected value. They are about whether the system preserves
important boundaries over time.

For example:

-   Did a discussion accidentally become formal state?
-   Did a recovered state get mistaken for current reality?
-   Did a useful pattern become an unauthorized rule?
-   Did a new capability solve a real problem or only add complexity?

This note describes a testing approach focused on those boundaries.

------------------------------------------------------------------------

## Start with a failure, not a feature

A common mistake is starting with:

> How do we test the whole AI system?

That question is usually too large.

A better starting point is:

> What specific failure are we trying to prevent?

For example:

Bad:

``` text
The AI should have good memory.
```

Better:

``` text
A temporary hypothesis discussed in a previous conversation
must not become formal Current unless it is explicitly adopted.
```

The second statement can be tested.

------------------------------------------------------------------------

## Behavioral slices

A behavioral slice is a small, observable piece of system behavior.

It should be:

-   specific;
-   repeatable;
-   isolated enough to diagnose;
-   connected to an actual failure mode.

Example:

``` text
Given:

Current = A

When:

The conversation discusses B
but B is never adopted

Then:

A later invocation should still resolve Current as A.
```

This test does not evaluate the whole project.

It evaluates one boundary:

``` text
Conversation
≠
Current State
```

Small tests make failures easier to understand.

------------------------------------------------------------------------

## Synthetic cases

Before testing complex real scenarios, use synthetic cases.

A synthetic case removes unnecessary variables.

Example:

``` text
Session 1:

Current = A


Session 2:

Discuss B

Do not adopt B


Session 3:

Ask:
"What is current?"
```

The purpose is not to prove production readiness.

The purpose is to answer:

> Does the mechanism behave correctly in a controlled situation?

Synthetic cases are especially useful for:

-   authority boundaries;
-   persistence decisions;
-   recovery behavior;
-   rule stability.

------------------------------------------------------------------------

## Test in the target host

A design that looks correct on paper may behave differently in the
environment where it runs.

AI behavior depends on factors such as:

-   model behavior;
-   instruction following;
-   context handling;
-   tool behavior;
-   file access;
-   platform limitations.

Therefore:

``` text
Design correctness
≠
Host behavior
```

A rule is not truly tested until it has been tested where the project
actually operates.

------------------------------------------------------------------------

## Record the exact failure

When a test fails, avoid descriptions that are too broad.

For example:

Bad:

``` text
The model is unreliable.
```

Useful:

``` text
The system correctly loaded Current=A,
but later summarized recent discussion
and treated B as the new Current.
```

The second description points toward a specific boundary failure.

The goal is not to blame the model.

The goal is to understand which responsibility failed.

------------------------------------------------------------------------

## Minimal rule changes

After identifying a failure, change as little as possible.

A common pattern in AI systems is:

``` text
Failure
→ add instruction
→ another failure
→ add another instruction
→ growing complexity
```

This can create a system that is harder to reason about than the
original problem.

A better approach:

``` text
Observed failure
→ smallest useful rule change
→ retest
```

For example:

If conversation can override Current, first strengthen the Current
authority boundary.

Do not immediately create:

-   another memory layer;
-   another state object;
-   another recovery mechanism.

Every new mechanism creates new interactions.

------------------------------------------------------------------------

## Retest the original failure

After changing the system, first rerun the case that exposed the
problem.

The first question is:

> Did the original failure disappear?

Not:

> What other improvements can we add now?

This keeps the development loop connected to evidence.

------------------------------------------------------------------------

## Interaction testing

A rule can work alone and still fail when combined with another valid
rule.

Example:

Rule 1:

``` text
Conversation cannot casually override Current.
```

Good.

Rule 2:

``` text
New evidence can update Current.
```

Also good.

But if Rule 1 becomes too strong:

``` text
Current can never change.
```

the system has created a new failure.

Interaction testing checks whether different mechanisms remain
compatible.

------------------------------------------------------------------------

## Regression cases

A solved failure should become a reusable test.

Example:

``` text
Test:

Repeated discussion of B
must not replace Current=A
without formal adoption.
```

Future changes to:

-   Working Logic;
-   System Contract;
-   host;
-   persistence strategy;

can be checked against the same case.

The regression suite should grow from observed failures, not from an
attempt to predict every possible failure.

------------------------------------------------------------------------

## Ablation: removing complexity

Testing should not only ask:

> What should we add?

It should also ask:

> What can we remove?

Ablation means temporarily removing or weakening a rule and checking
whether behavior actually becomes worse.

Example:

``` text
Remove rule X

Run previous regression cases

Observe whether behavior changes
```

If nothing breaks, the rule may be:

-   redundant;
-   obsolete;
-   covered by a higher-level principle.

This prevents permanent accumulation of unnecessary complexity.

------------------------------------------------------------------------

## Adversarial cases

Some boundaries are only visible when the system is strongly tempted to
cross them.

Examples:

### Current authority

Normal case:

``` text
Discuss B once.
```

Adversarial case:

``` text
Discuss B repeatedly
with much more text than A.
```

Question:

Does repetition create false authority?

------------------------------------------------------------------------

### Evidence independence

Normal case:

``` text
One report supports C.
```

Adversarial case:

``` text
Ten reports support C,
but all originate from the same source.
```

Question:

Does quantity get mistaken for independent evidence?

------------------------------------------------------------------------

### Rule promotion

Normal case:

``` text
One useful observation appears.
```

Adversarial case:

``` text
Many similar observations appear quickly.
```

Question:

Does the system automatically convert a pattern into a production rule?

------------------------------------------------------------------------

## Validation maturity

Testing claims should remain precise.

These are different states:

``` text
Designed

Synthetic-tested

Target-host tested

Interaction-tested

Regression-protected

Production-observed
```

They should not be collapsed.

A design document may describe a mechanism.

A synthetic test may show a narrow behavior.

A host test may reveal environment-specific problems.

Production observation may reveal entirely different conditions.

Each level provides different evidence.

------------------------------------------------------------------------

## What this testing method tries to avoid

### Testing everything at once

Large tests make failure attribution difficult.

### Testing only happy paths

Boundaries usually fail under pressure.

### Adding rules without removing old ones

Complexity grows faster than understanding.

### Treating passing one test as proof of general correctness

A test proves something about a case.

It does not prove everything.

------------------------------------------------------------------------

## The testing loop

The process can be summarized as:

``` mermaid
flowchart LR
    P[Observed Problem]
    B[Behavioral Slice]
    S[Synthetic Case]
    T[Target Host Test]
    F[Failure Analysis]
    M[Minimal Change]
    R[Retest]
    G[Regression]
    A[Ablation]

    P --> B --> S --> T --> F --> M --> R --> G --> A
```

The purpose of testing is not to prove that a long-running AI project is
finished.

It is to keep the relationship between:

``` text
Problem

Design Decision

Implementation

Observed Behavior
```

traceable.

When complexity enters the system, there should be a reason for it.

When a rule remains, there should be evidence that it still earns its
cost.
