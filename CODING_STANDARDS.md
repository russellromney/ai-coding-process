# Coding standards

Guidelines for AI coders across all projects. Per-project overrides go in the project's own `CLAUDE.md` (or equivalent).

## Communication style

- Short and direct. No hedging ("I think maybe"), no lengthy explanations where a sentence will do.
- **Act when obvious, ask when consequential.** Default to editing — the codebase is yours to maintain. Ask before spending significant tokens, making irreversible moves, or picking between non-obvious approaches. A cheap check-in beats the wrong direction.
- Speak up when you notice friction, missing workflow, or a better way to organize the work. Don't wait to be asked.
- Quality over token efficiency. Never delegate judgment-heavy work to cheaper models. Never cut corners to save tokens.

## Think before coding

- State assumptions explicitly before implementing. If multiple interpretations exist, present them. Don't pick silently.
- Transform vague requests into verifiable goals: "fix the bug" → "write a test that reproduces it, then make it pass."
- Every changed line should trace directly to the request. Don't improve adjacent code, comments, or formatting unless asked.
- Match existing style, even if you'd do it differently.
- If you notice unrelated issues, mention them. Don't silently fix them.

## Designing abstractions

**Shape before mechanics.** Before introducing a trait, enum, or shared type, ask what the shape is actually abstracting over. A trait with one impl is a wrapper, not an abstraction. An enum where callers always pattern-match on the variant is a tagged dispatch table. If every variant of a shared type carries consumer-specific fields and only coordination metadata is genuinely shared, the abstraction is thin — prefer opaque payload + per-consumer codec over variant explosion.

**Opacity at crate boundaries.** Structured types across crate boundaries couple release cycles: a field added to one side forces a bump in the other. Pay that cost only where genuine cross-crate consensus exists (protocol envelopes, coordination metadata). For consumer-specific data, prefer opaque bytes + private codec; it keeps the boundary narrow and lets each side evolve independently.

## Development workflow

Non-trivial work follows a plan → execute → verify loop. Plan with the expensive model, execute with a cheap one, verify with commands. Process, template, and handoff skeleton live in `planning.md`. Plans per project go in that repo's `ROADMAP.md`.

For each step:
1. Implement the feature.
2. Write comprehensive tests (happy path, negative, edge case, integration, e2e).
3. Review for bugs, halfwork, or issues. Fix and add regression tests.
4. Review at a high level: does this make sense to a user? Does the developer experience make sense? Does the implementation match the design?
5. Discuss with the user if uncertain.
6. Continue to the next step.

## Testing and reliability

"Your job is to deliver code you have proven to work" (Simon Willison).

- Every feature ships with comprehensive tests: happy path, failure modes, edge cases, integration, e2e.
- Tests run against real infrastructure, not mocks. Mocks hide the bugs that matter.
- **Skipped tests are failed tests.** Pre-existing failures are bugs — fix them, don't step around them. If infrastructure is missing, add it; if the code can't be tested, redesign it so it can.
- 100 tests that catch real bugs beat 1000 that check boxes.

## Fail fast, no silent failures

- **Critical fields must be REQUIRED.** Don't silently fall back to defaults for important data.
- **No silent failures.** If an operation fails, it must be visible: log at error level, propagate the error, or crash. Never warn and continue as if nothing happened.
- Background tasks must use exponential backoff and escalating log severity on repeated failures.

## Multi-agent safety

Multiple agents may run concurrently on the same machine and repos. These rules prevent agents from breaking each other.

**Services:**
- **NEVER start, stop, kill, or restart local services.** Only the user manages service lifecycle. This includes: `nohup`, `kill`, `pkill`, docker start/stop, or any other process management.
- If a service needs restarting to pick up code changes, tell the user what to restart and why.

**Git:**
- **NEVER run git commands without explicit user permission.** Always ask first.
- **NEVER use `git stash`.** It can silently clobber uncommitted work from another agent. Always commit changes instead.
- **NEVER use `git checkout -- <file>` or `git restore <file>` to discard uncommitted changes.** Discarding files may destroy another agent's work irrecoverably. If another agent's changes cause compilation errors, work around them or ask the user.

## Commits and pull requests

LLMs make it easy to ship huge, multi-topic PRs with 1000+ LOC diffs. This is a bad habit; it makes review impossible, `git bisect` useless, and rollback destructive. Pre-factor changes into atomic commits.

Principles (adapted from the Turso project's contribution guide):

- **One PR = one thing.** A good PR fixes a specific bug or adds a single feature with its tests. A bad PR fixes a bug, adds an unrelated feature, and refactors a helper along the way.
- **Commits tell the story.** Break a PR into commits so a future developer bisecting a nasty bug can reconstruct what happened. Someone reading the log should understand the intent of each step.
- **Don't mix logic, cleanup, and formatting.** Refactors are their own commits. Format-only commits never touch behavior.
- **Write clear, short commit messages.** If the message is easy to write, the commit is atomic. If you're reaching for "and" or listing three things, it should be two or three commits.

Before a multi-topic change, plan the commit sequence up front. If you made a large batch of edits and are about to stage everything as one commit, stop and split by topic — typically (a) the core change, (b) related tests, (c) any pre-existing issues fixed along the way, (d) docs. Use `git add <file>` to stage by file; resist the urge to "just also fix" an adjacent smell — note it and handle it separately.

## Conventions

### Code style
- Concise over verbose. No over-engineering or premature abstractions.
- **Keep files under 1000 lines.** Split when approaching this limit. Group by domain, separate concerns.

### Memory: README + ROADMAP + CHANGELOG
- **README.md** — project description, how to run, deploy, test
- **ROADMAP.md** — plans for features. New work goes here first. **Remove** completed items (don't just check them off)
- **CHANGELOG.md** — summaries of completed work, moved from ROADMAP
- **No other .md files** — no scratchpads, session handoffs, or feature plans. Use ROADMAP.

## Operational

### Make commands
Always use the project's `make` commands if they exist. Don't invoke tools directly:
```bash
make up / down / test / deploy
```

### Package managers
Run through the project's package manager:
```bash
# Python
uv run pytest tests/

# Node
npm run test
```

### Secrets
- Never commit secrets.
- Use the project's secrets tool (Doppler, Infisical, Soup, 1Password CLI, etc.) — check the README or CONTRIBUTING for which one.
- `<tool> run -- <cmd>` injects secrets as env vars.
