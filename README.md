# ai-coding-process

How I work with AI coders. Two docs:

- **[planning.md](./planning.md)** — the plan → execute → verify loop. Gap-based spec density, 4-beat workflow, cold-handoff template, trap compounding.
- **[CODING_STANDARDS.md](./CODING_STANDARDS.md)** — coding guidelines that apply across projects. Testing, fail-fast, multi-agent safety, commit conventions.

The short version: plan with the most capable model you have, execute with a cheaper capable one once the plan is locked, verify with commands not vibes. Traps found in one project compound into every subsequent plan template — known bugs are forward-looking knowledge that survives the sessions that discovered them.

## Setup

```bash
git clone git@github.com:russellromney/ai-coding-process.git ~/Documents/Github/ai-coding-process
```

Then point your agent config at it. For Claude Code:

```bash
# planning.md as the canonical source; symlink into ~/.claude/
ln -sf ~/Documents/Github/ai-coding-process/planning.md ~/.claude/planning.md
```

Your `~/.claude/CLAUDE.md` (or equivalent) should have a line telling the agent where to find the planning doc, e.g.:

> Non-trivial work follows a plan → execute → verify loop. Process and templates live in `~/.claude/planning.md`. Plans per project go in that repo's `ROADMAP.md`.

The standards doc is for reference. Either adapt pieces into your global agent config, or link to it from there.

## Adapting

The docs are opinionated and first-person. Fork freely and swap in your own conventions. The parts worth keeping as-is:

- The gap framing (spec density matches the context gap between planner and executor)
- The 7-section cold-handoff template
- Trap compounding — the habit of naming every bug you've seen once so downstream plans avoid it
- Verify with commands, not reports

The rest is taste.
