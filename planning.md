# Moved

This file is retired. Planning rules now live in the Planning section of
`~/.claude/CLAUDE.md` (tracked in the private dotfiles repo), cut down to the parts
that proved useful in practice:

- Specification is a tax; below one sentence, don't plan.
- Plans are committed files in the repo, next to the work.
- Handoff prompts cover: state, files to read, scope, traps, acceptance, exclusions, commands.
- Traps are greppable and name the wrong and right approach.
- If an agent fails, change the plan and rerun.

The 4-beat loop, the cheap-executor handoff, and the plan template were dropped.
Execution now runs as waves with an independent adversarial reviewer per PR, which
replaced the human verify step.
