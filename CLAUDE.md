# CLAUDE.md

A debugging protocol for the human and the AI working together. Guidance you give Claude (or follow yourself) when something is broken. Merge with project-specific instructions as needed.

**The operational embodiment of [vibe-engineer-skills](https://github.com/HermeticOrmus/vibe-engineer-skills)**: that repo's first principle is "hypothesis before help." This file is what that principle looks like as a step-by-step procedure when you sit down to fix a bug.

**Tradeoff**: This protocol biases toward finding the root cause over shipping a fast patch. For a one-line typo with an obvious cause, use judgment and skip ahead.

## The rule

Do not write a fix until you have a ranked hypothesis table and a confirmed root cause. A fix written before the cause is known is a guess wearing the costume of a solution.

## Phase 1: Reproduce

Establish a reliable reproduction before anything else. A bug you cannot trigger on demand is a bug you cannot prove you fixed.

- What exact steps trigger the issue?
- Is it consistent or intermittent? Intermittent points at state, timing, or environment, not pure logic.
- What is the exact error message and stack trace? Read it fully, not just the first line.
- What is the expected behavior versus the actual behavior?

If you cannot reproduce it, that is the first thing to solve. Gather the error, the steps, and the environment until the failure is repeatable.

## Phase 2: Isolate

Narrow the scope until the bug has nowhere to hide.

- When did it start? Recent commits are the highest-yield suspect: `git log --oneline -10`.
- What changed? Diff the suspect range. A bug that appeared after a known change usually lives inside that change.
- What is the minimal case? Strip the reproduction down to the smallest input, the fewest lines, the simplest call that still fails.

Isolation converts "the app is broken" into "this function returns the wrong value for this input." The second statement is debuggable; the first is not.

## Phase 3: Hypothesize

Write a ranked hypothesis table BEFORE touching the code. This is the step that separates debugging from guessing.

| # | Hypothesis | Likelihood | How to test |
|---|-----------|------------|-------------|
| 1 | Most likely cause, stated specifically | High | A specific, cheap test that confirms or refutes |
| 2 | Second possibility | Medium | A specific test |
| 3 | Less likely cause | Low | A specific test |

Rules for the table:

- Each hypothesis names a concrete cause ("the S3 client is configured for the wrong region"), not a vague area ("something with uploads").
- Each row has a test that can actually run and produce a yes/no answer.
- Rank by likelihood given the evidence from Phase 2, not by ease of fixing.

If you are using an AI to help, hand it this table. A hypothesis table turns the AI's job from inventing a cause into testing yours. That is the whole point of "hypothesis before help."

## Phase 4: Test

Work the table top to bottom.

1. Take the most likely hypothesis.
2. Run its test.
3. Record the result.
4. If confirmed, you have the root cause. Proceed to Phase 5.
5. If refuted, cross it out and move to the next row. Do not skip ahead to a fix.

A refuted hypothesis is progress, not failure. It removes a suspect. If every row is refuted, the table was wrong; return to Phase 2 with what you learned and write a new one.

## Phase 5: Fix

Only now, with a confirmed root cause, do you write the fix.

1. **Minimal fix**: the smallest change that addresses the confirmed cause. Not the change that touches the most code; the change that touches the cause.
2. **Side effects**: what else calls this code? What else depends on the behavior you are changing? Trace the callers.
3. **Verify**: run the reproduction from Phase 1. The fix is not done until the thing that failed now passes.
4. **Regression test**: add a test that reproduces the original bug and now passes. Without it, the bug can return silently in a future change.

### Root cause, not workaround

The fix must address the confirmed cause from Phase 4, not the symptom. These are workarounds wearing a fix costume; reject all of them:

- A try/catch that swallows the failure mode instead of preventing it.
- A defensive check that masks an upstream contract violation.
- Renaming the symptom variable instead of correcting the state it holds.
- Mocking or stubbing the broken dependency instead of fixing it.
- Asserting the bug away (`assert x is not None` where the real question is why `x` is None).

A workaround that passes the test is worse than no fix: it hides the bug and propagates it to every other caller of the same broken code.

## Phase 6: Document

Close the loop with a short record. This is what turns a fixed bug into a lesson.

```text
## Root cause
What actually caused the issue, in one or two sentences.

## Fix applied
What was changed, and why it addresses the cause and not the symptom.

## Prevention
The regression test added, plus the category of bug and how to catch
this class faster next time.
```

---

## Quality check

After the fix, rate the process honestly:

- Did you find the actual root cause, or settle for a workaround that happened to make the symptom disappear?
- Is the fix minimal and aimed at the cause, or did it sprawl?
- Is there a regression test that fails on the old code and passes on the new?

If the answer to any of these is unsatisfying, the bug is not closed. It is hidden.

---

**Source**: This file packages the "Systematic Debug Protocol" as the operational form of the [vibe-engineer-skills](https://github.com/HermeticOrmus/vibe-engineer-skills) first principle, "hypothesis before help." Companion to [andrej-karpathy-skills](https://github.com/HermeticOrmus/andrej-karpathy-skills), which governs how the AI should behave while you run this loop.

**License**: MIT. Use it, fork it, merge it into your own.
