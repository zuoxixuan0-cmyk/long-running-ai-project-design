# Principles

I originally had a much longer list of principles. Most of them turned out to be consequences of a smaller set.

These are the ones I currently use most often when reviewing a long-running AI project.

## 1. Do not make the host carry the whole project

The host is replaceable. Formal continuity should not exist only in conversation context, model memory, or implicit platform behavior.

A host change may require compatibility testing, but it should not silently redefine the project.

## 2. Give Current an explicit authority when continuity depends on it

If a future invocation needs to know what the project formally holds now, it should not have to infer that from recency, tone, or conversational context.

Current should be identifiable.

That does not make it true forever. It only makes it the formal state that the system knows it must verify or revise.

## 3. Keep authority separate from truth

A state can be authoritative and still be stale.

A source can be official and still be wrong.

A stored conclusion can be internally consistent and still fail against new evidence.

Persistence creates continuity; it does not create correctness.

## 4. Let reality defeat the schema

A schema is an encoding tool.

If evidence does not fit cleanly, I would rather preserve uncertainty, revise the structure, or leave a state incomplete than force reality into a category that makes the data look tidy.

Unknown is a valid state.

## 5. Persist conclusions with future value, not every trace of computation

Search queries, intermediate rankings, temporary hypotheses, and tool logs may be useful during a run without deserving long-term status.

The question is not “did this happen?”

It is “will a future invocation need this cognition in order to continue well?”

That keeps persistent state smaller and reduces the chance that old execution debris becomes new context.

## 6. Allow useful runs to end without a write

Persistence should not be a ritual.

A run may confirm that nothing material changed. It may produce a useful answer that has no cross-run value. It may leave an open question unresolved.

All of those can be successful outcomes.

If every invocation is expected to create or update something, state quality usually degrades.

## 7. Keep cognition open and rules governed

The project should be able to change its mind when reality changes.

Its production rules should be harder to change than its current understanding.

That difference is deliberate. Runtime learning may generate candidate improvements, but promotion into stable rules should go through testing and governance.

## 8. Make complexity earn its place

I try not to add a mechanism because the architecture feels incomplete without it.

History is useful when past state matters. Compression is useful when scale becomes a real problem. Concurrency controls are useful when there are actually multiple writers.

Until then, the mechanism is mostly maintenance cost and interaction surface.

This principle applies to testing infrastructure too. A project does not become more reliable just because it has more machinery.

## A practical review checklist

When I review a design, I usually ask questions like these:

- Is this formal state, or only conversation context?
- If it is formal state, where does its authority come from?
- Is the state authoritative, or merely likely to be true?
- Is this worth carrying into another invocation?
- If it is worth carrying forward, does the contract already have a valid place for it?
- Is this new mechanism solving an observed failure?
- Did the system learn something, or did the production rules actually change?
- If the project resumed after a gap, what still needs to be revalidated?

These questions are more useful to me than a large checklist of mandatory modules.
