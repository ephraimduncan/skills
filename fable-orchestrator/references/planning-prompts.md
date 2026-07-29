# planning prompts

the prompt pasted into every Fable plan session. fill the placeholders, drop sections that don't apply, keep the structure — it is what makes the plans worth dispatching.

## template

```
<task spec: the objective, verbatim from the user where possible>

Context:
- repo: <path>, branch <branch> off main
- relevant files/areas: <paths>
- constraints: <what must hold>
- do NOT touch: <paths/behaviors>
- <known history: reverted approaches, legacy caveats, prior art to reuse>

Before planning, do a blindspot pass on the areas this touches: surface unknown
unknowns, unwritten conventions, hidden coupling, and relevant history. Fold each
one into the plan as a numbered constraint.

If anything is still ambiguous after that, interview me: ask one question at a
time, prioritized by architectural blast radius — questions whose answer would
change the architecture come first.

Then write the implementation plan, sorted by likelihood-of-tweaking, not
execution order:

- Section A — judgment calls I might disagree with, most tweakable first. For
  each: the chosen option, one alternative, the tradeoff, and a one-line
  instruction for switching to the alternative.
- Section B — the build sequence.
- Section C — mechanical work, collapsed; I trust you on that part.

Ground everything in the real codebase: cite actual file paths, flags, and
existing utilities to reuse. Name anti-patterns as explicitly as patterns.
State exact acceptance criteria and the exact test/verify commands that must go
green — they become the implementer's definition of done and the reviewer's
checklist.

Flag the weakest part of this plan explicitly.
```

## frontend/design additions

when the task is user-facing, the spec section must also carry design intent — layout, states (empty/loading/error), interactions, spacing, motion, and reference patterns to match. taste lives in the spec and in validation, not in the implementer.

## hardest parts

for the riskiest or most intricate piece of the task, ask the planner for pseudocode-level detail in Section B. if a worker later struggles, the fix is usually more pseudocode in the spec, not a smarter worker.
