# Planning

My process for planning and executing software work with AI.

## The principle

Specification is a tax. Pay only what the gap between my context and the executor's demands.

Below one sentence: don't plan. Typo fixes and trivial edits don't need it.

## Why plan

Writing a plan isn't just about handing off to a cheap executor. The act of writing forces enumeration — ambiguities and missing specs that would otherwise show up at build time show up while the plan is being written. Planning is a thinking tool first, a cost lever second.

The cost lever is real. A well-specified cold handoff runs roughly 5-10x cheaper than doing the whole task in an expensive model end-to-end.

## The 4-beat loop

What I actually do when a task needs a plan.

### Beat 1 — initiate planning (me → Opus)

One sentence:

> Plan [task], save to ROADMAP.md.

Opus reads this doc for the template, scouts the codebase for references and traps, drafts the 7 sections, writes to ROADMAP.md. Opus should ask at most one clarifying question before writing — not ten.

If the codebase has prior plans, a TRAPS file, or related design notes, Opus pulls from those first.

### Beat 2 — review and refine (me → Opus)

Short directives in the same session:

> Traps list is thin. Grep `fix:` commits in the relevant area.
> Acceptance should require CI green, not just local.
> Out-of-scope: don't touch adjacent module.

Opus updates ROADMAP.md in place. Iterate until self-check passes.

### Beat 3 — hand off (me → cheap model)

New session with the execution model (Kimi, GLM, Qwen, etc.). Paste the plan and tell it to execute:

> Read `~/.claude/CLAUDE.md` and the repo's CLAUDE.md for coding conventions. Then execute the plan below. Autonomous — land code, verify, ship. Report when done.
>
> [ROADMAP.md section pasted]

The read-CLAUDE.md line matters when the executor isn't running in a Claude Code session with those files already loaded. In Claude Code they're implicit; elsewhere they're not, and the plan should never assume them.

If Opus wrote a standalone handoff prompt in beat 2, use that directly. Self-contained prompts are safer because they wrap the plan with the executor framing (autonomous, ship, report) that ROADMAP might not have.

### Beat 4 — verify and close (me)

Pull any updated submodule. Run the acceptance commands. Grep for each listed trap — did the executor actually avoid it? Check CI. Diff against the nearest reference.

Assume ~10% of any executor's report is aspirational until verified.

If broken: diagnose, update the plan *permanently* (not just for this retry), re-run beat 3. Don't re-prompt without a plan change.

## Plans live in ROADMAP.md

Per project. Version-controlled with the code. Completed plans move to CHANGELOG.md (or get deleted).

## Traps compound

The most valuable thing across plans: known bugs I've already seen. Every trap should appear in every subsequent plan where that category applies. Find once, avoid N times.

A good trap entry:
- Specific enough to grep for in the prompt or the output
- Names the wrong thing a naive implementation would do
- Names the right thing instead

A bad trap entry is a vague warning ("be careful with concurrency").

Categories that recur across projects:
- API idiosyncrasies — methods that don't match their name, version-dependent behavior
- Concurrency ordering — lock order, wake-channel polling order
- Silent failure modes — errors deferred to first use, swallowed fallbacks
- Platform quirks — crippled system libraries, toolchain divergence
- Build system churn — API changes between minor versions

If the list grows past ~10 per project, pull it into `TRAPS.md` in the repo. The point is persistence.

## The cold-handoff template

Opus populates this during beat 1. Seven sections; each exists because its absence causes drift.

1. **Context** — current state, paths, existing surface, test state, in-flight work to avoid
2. **References** — paths to read first
3. **Scope** — every method, struct, file, exhaustively; signatures included
4. **Traps** — every known gotcha
5. **Acceptance** — verifiable checks (test count, CI, concrete assertions)
6. **Out of scope** — explicit NOTs
7. **Commands** — exact build / test / ship lines, env setup included

## Skeleton

For beat 3. Wrap the ROADMAP content with this framing:

```markdown
Read `~/.claude/CLAUDE.md` and any project CLAUDE.md for coding conventions.

You're [goal] in [path]. Autonomous — land code, verify, ship.

## Context
[state, surface, test count, in-flight work to avoid]

## References (read first)
- [path] — [why]

## Scope
1. [specific signature / change]

## Traps
- [known gotcha]

## Acceptance
- [verifiable check]

## Out of scope
- Don't [thing]

## Commands
[exact build + test + ship]

Report: [what to summarize]
```

## Anti-patterns

In any beat:
- Single-verb specs ("refactor X")
- Unverifiable acceptance ("feels idiomatic")
- Orphan references ("use the existing pattern")
- Unflagged traps
- Nested TODOs in the prompt ("figure out the JSON approach")

Parallel handoffs:
- Don't go wider than you can review
- Don't parallelize when tasks share mutable state (same file, same version, same submodule branch)

## Self-check before beat 3

1. Could someone without context execute this?
2. Are signatures and known bugs named?
3. Is "done" verifiable without my judgment?
4. Are destructive actions explicitly allowed or forbidden?

Any "no" — rewrite.

## Minimum viable ritual

Before a cold handoff, read the plan back. Reading it catches more bugs than any checklist.
