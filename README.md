# ai-coding-process

How I work with AI coders.

- **[planning.md](./planning.md)**: Plan → execute → verify loop. Gap-based spec density, 4-beat workflow, cold-handoff template, trap compounding.
- **[CODING_STANDARDS.md](./CODING_STANDARDS.md)**: Global guidelines. Testing, fail-fast, multi-agent safety, commit conventions.

Plan with capable models, execute with cheap ones, verify with commands. Traps compound into templates.

## Setup

Clone to a predictable location:

```bash
git clone git@github.com:russellromney/ai-coding-process.git ~/Documents/Github/ai-coding-process
```

Symlink `planning.md` into your agent config so it loads automatically:

```bash
ln -sf ~/Documents/Github/ai-coding-process/planning.md ~/.claude/planning.md
```

Make `CODING_STANDARDS.md` the canonical general rules — don't duplicate its content in your agent config. A minimal `~/.claude/CLAUDE.md` (or equivalent) looks like:

```markdown
# Personal agent config

General coding rules live in:
- ~/Documents/Github/ai-coding-process/CODING_STANDARDS.md
- ~/.claude/planning.md (workflow)

Read those first. What follows is personal overrides and local infra.

## My terminal / secrets / dotfiles setup
[personal stuff here]
```

The public repo stays the single source of truth for general rules; your private config is just the personal deltas. No duplication, no drift.

## Adapting

Fork and swap conventions. Keep these:

- **Gap framing**: Spec density matches the context gap between planner and executor.
- **7-section cold-handoff template**.
- **Trap compounding**: Name every bug once to avoid it forever.
- **Verify with commands**, not reports.
