# Coding standards

Global guidelines for AI coders. Your personal agent config (e.g., `~/.claude/CLAUDE.md`) may add overrides on top; per-project overrides go in that project's `CLAUDE.md`.

## Communication style

- **Short and direct.** No hedging or filler.
- **Act when obvious, ask when consequential.** Default to editing. Ask before spending significant tokens, making irreversible moves, or picking between non-obvious approaches.
- **Report friction.** Mention missing workflow or better organization immediately.
- **Quality over token efficiency.** Never delegate judgment-heavy work to cheaper models.

## Think before coding

- **State assumptions explicitly.** Present interpretations; don't pick silently.
- **Verifiable goals.** "Fix bug" → "Write test that reproduces it, then make it pass."
- **Traceability.** Every line must trace to the request. Don't improve adjacent code unless asked.
- **Match existing style.**
- **Mention unrelated issues.** Don't silently fix them.

## Designing abstractions

- **Shape before mechanics.** A trait with one impl is a wrapper. An enum where callers always pattern-match is a tagged dispatch table. Prefer opaque payloads + private codecs over variant explosion for consumer-specific data.
- **Opacity at crate boundaries.** Structured types across boundaries couple release cycles. Use only for genuine consensus (protocols, coordination). Use opaque bytes + private codecs for consumer-specific data.

## Development workflow

Plan with expensive models, execute with cheap ones, verify with commands. Templates in `planning.md`. Project plans in `ROADMAP.md`.

1. Implement feature.
2. Write comprehensive tests (happy path, negative, edge case, integration, e2e).
3. Fix bugs and add regression tests.
4. Review UX, DX, and implementation vs design.
5. Discuss if uncertain.
6. Continue.

## Testing and reliability

"Your job is to deliver code you have proven to work" (Simon Willison).

- **Comprehensive tests.** Ship happy path, failure modes, edge cases, integration, and e2e tests.
- **Real infrastructure.** No mocks.
- **No skipped tests.** Pre-existing failures are bugs; fix them. Redesign code if untestable.
- **Quality over quantity.** 100 tests catching real bugs beat 1000 checking boxes.

## Fail fast, no silent failures

- **Required critical fields.** No defaults for important data.
- **Visible failures.** Log at error level, propagate errors, or crash. Never warn and continue.
- **Exponential backoff.** Use for background tasks with escalating log severity.

## Multi-agent safety

- **Services:** NEVER start, stop, or restart local services (docker, nohup, etc.). Tell the user what to restart and why.
- **Git:**
  - Ask before running any git command.
  - NEVER use `git stash`. Commit instead.
  - NEVER use `git checkout -- <file>` or `git restore` to discard changes.

## Commits and pull requests

Atomic commits only.

- **One PR = one thing.** One bug fix or feature with tests.
- **Commits tell the story.** Break PRs into commits that reconstruct intent.
- **No mixed logic.** Refactors, cleanup, and formatting get their own commits.
- **Clear, short messages.** If it's hard to write, the commit isn't atomic.

Plan commit sequence up front. Split by: (a) core change, (b) tests, (c) pre-existing fixes, (d) docs. Use `git add <file>` to stage by file.

## Conventions

- **Code style:** Concise. No over-engineering. Files under 1000 lines.
- **Memory:**
  - `README.md`: Description, run, deploy, test.
  - `ROADMAP.md`: Feature plans. New work goes here first. Remove completed items.
  - `CHANGELOG.md`: Summaries of completed work.
  - No other `.md` files.

## Operational

- **Make:** Use `make` commands. Don't invoke tools directly.
- **Package managers:** Run through project's manager (e.g., `uv run`, `npm run`).
- **Secrets:** Never commit secrets. Use project's tool (Soup, Doppler, etc.). `<tool> run -- <cmd>` injects env vars.
