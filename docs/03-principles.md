# Principles

These principles are a smaller set of design rules that I use when
evaluating long-running AI project behavior.

They are not a required framework.

They are decision boundaries that help answer questions such as:

-   Should this information persist?
-   Should this state be trusted?
-   Does this failure require a new mechanism?
-   Did the project learn something, or did the system rules actually
    change?

The goal is not maximum memory or maximum automation.

The goal is maintaining clear boundaries as a project continues over
time.

------------------------------------------------------------------------

## 1. Separate Context from State

Conversation, documents, observations, and previous discussions can all
provide useful context.

They are not automatically formal project state.

A project needs a clear answer to:

> What does the system formally hold as current?

Without that separation, recent information can accidentally replace
accepted state simply because it is newer or more detailed.

------------------------------------------------------------------------

## 2. Separate Authority from Truth

A state can be authoritative without being permanently true.

For example:

``` text
Current = B
```

means:

> This is the formal state currently held by the system.

It does not mean:

> External reality can never change away from B.

Authority defines where the system looks for a formal answer.

Validation determines whether that answer still matches reality.

------------------------------------------------------------------------

## 3. Persist Meaning, Not Every Trace

Not everything produced during a run deserves a place in persistent
state.

Temporary analysis, intermediate searches, abandoned ideas, and
execution details may help the current run without helping future runs.

A useful question is:

> Will a future invocation need this information to continue correctly?

If the answer is no, persistence may create more noise than value.

------------------------------------------------------------------------

## 4. Keep Runtime Cognition Separate from Rule Changes

A project can learn something during execution.

That does not mean the production rules should immediately change.

A useful distinction:

``` text
Observation
→ Candidate improvement
→ Testing
→ Governed rule change
```

Runtime cognition should be able to reveal possible improvements without
silently rewriting the system that generated it.

------------------------------------------------------------------------

## 5. Let Complexity Follow Observed Failure

A mechanism should exist because a real problem requires it.

Not because the architecture feels incomplete without it.

Examples:

-   History is useful when historical paths matter.
-   Recovery systems are useful when interruptions create real problems.
-   Concurrency controls are useful when multiple writers exist.

Additional capability creates additional maintenance cost and
interaction surface.

Complexity should earn its place.

------------------------------------------------------------------------

## 6. Preserve Uncertainty When Evidence Is Insufficient

A system should not force certainty simply because a value is required.

Sometimes the correct state is:

``` text
Unknown
```

Unknown can represent:

-   insufficient evidence;
-   unresolved conflict;
-   incomplete recovery;
-   unavailable validation.

A false certainty is often more damaging than an explicit uncertainty.

------------------------------------------------------------------------

## A practical review checklist

When reviewing a long-running AI project, I ask:

-   Is this context or formal state?
-   If it is state, where does its authority come from?
-   Is the state still valid, or only historically valid?
-   Does this information have future value?
-   Is this change solving an observed failure?
-   Is this a cognition change or a rule change?
-   Is the added complexity justified by evidence?

These questions are more useful than a list of mandatory modules.

------------------------------------------------------------------------

## The common pattern

Many failures in long-running AI systems come from collapsing things
that should remain separate:

``` text
Context
≠
State
```

``` text
Authority
≠
Truth
```

``` text
Learning
≠
Rule Change
```

``` text
Capability
≠
Obligation
```

Keeping these boundaries visible makes the system easier to test,
recover, and evolve.
