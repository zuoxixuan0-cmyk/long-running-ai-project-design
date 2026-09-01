# Problem Model

The easiest way to misunderstand this design is to start with the architecture.

I did not begin by deciding that a long-running AI project should have a contract layer, working logic, and persistent state. Those boundaries appeared because several kinds of failure kept recurring once projects had to continue across separate invocations.

This document describes the five failure classes that mattered most.

## 1. Conversation can look like state even when it is not

A conversation is full of useful information, but it is a poor place to infer formal current state.

Imagine a project that ends one session with a decision recorded as **A**. A later session spends most of its time exploring **B** as an alternative, but B is never accepted. A third invocation sees the more recent discussion, gives it too much weight, and answers as though B became the project state.

Nothing was “forgotten” in the usual sense. The information about A may still be present.

The failure is that recent context has been mistaken for authority.

That is why I treat Current as an explicit semantic role whenever later invocations depend on it. The implementation can vary. The important point is that a future run should not have to reconstruct formal state by reading the tone or recency of a conversation.

## 2. Recovering the last state does not tell you what is true now

Explicit state solves one problem and immediately creates another.

Suppose the project records A on day one, then does not run for ten days. When it resumes, the stored state is still A. But the outside world may have moved from A to B and then to C.

The recovery mechanism can work perfectly and still give the system stale knowledge.

This is why I separate **state recovery** from **revalidation**.

My default sequence is:

1. restore the last formal state;
2. notice that a time gap exists;
3. revalidate the current endpoint;
4. reconstruct the path through the gap only if the current task actually needs it.

The fourth step matters. A project does not always need a complete reconstruction of everything that happened while it was inactive. Sometimes it only needs to know where reality stands now.

That keeps recovery useful without turning every gap into a full historical research project.

## 3. History becomes dangerous when it competes with Current

History is valuable precisely because old states should not disappear. But durable history can become a problem if the runtime treats it as permanently active context.

The failure usually appears gradually. Older material is often more detailed than the current state because it has accumulated more evidence, explanation, and commentary. If everything is loaded together, historical detail can begin to dominate the project’s present interpretation.

A long-running system then loses a basic distinction:

- *this used to be the project’s view*;
- *this is what the project holds now*.

I therefore keep History and Current separate in responsibility. History may be durable without being loaded on every run.

Some projects do not need a History layer at all. If the only important requirement is “resume from the latest formal state,” Current plus recovery information may be enough.

## 4. Reusable knowledge can become an unearned authority

A project that runs for long enough will notice recurring patterns. That is useful. Reusing every prior conclusion as a rule is not.

Suppose several previous cases shared condition X and were followed by outcome Y. The project may reasonably store that as a reusable prior. Later, a new case contains X but also a new condition Z. If the old pattern is applied automatically, the stored experience can shape the interpretation before the current evidence has had a fair chance to disagree.

Over time this can become self-reinforcing: the prior influences interpretation, the interpretation resembles the prior, and the prior appears to have been confirmed again.

For that reason, I treat stored knowledge as defeasible. It can guide attention and provide useful priors, but current evidence must be able to revise, narrow, or invalidate it.

This is one reason I do not use “knowledge” and “authority” as synonyms.

## 5. Runtime learning should not silently become production behavior

The last problem is governance.

AI systems are good at finding patterns and proposing improvements. That makes it tempting to let a long-running project modify its own instructions whenever it “learns” something.

The danger is easy to miss.

A single run observes a local failure. The system generalizes from it and writes a new global rule. Future runs now behave according to that rule, which makes similar observations more likely. A local mistake has become a production feedback loop.

I want runtime cognition to be able to *surface* a design issue without being able to *promote* that issue directly into production rules.

A safer route is:

```text
runtime observation
→ candidate design issue
→ focused test
→ evidence
→ governed rule change
```

The exact governance mechanism can vary. The important boundary is that learning and rule promotion are not the same operation.

## What these failures have in common

All five failures involve two things being collapsed into one:

- conversation and state;
- saved state and current reality;
- history and current state;
- reusable knowledge and authority;
- learning and rule change.

Once those distinctions matter, a chat-centric model of the project becomes too weak.

That is where the architecture in the next document comes from. It is not an attempt to impose a four-part framework on every AI project. It is a way to keep responsibilities separate when long-running behavior makes those separations necessary.
