# skills

Personal agent skills for the open [skills.sh](https://skills.sh) ecosystem. Includes **pstack**, a harness-neutral fork of [Cursor's pstack](https://github.com/cursor/plugins/tree/main/pstack).

## Install

```bash
# all skills in this repo
npx skills add MondayNotAGoodDay/skills

# one skill
npx skills add MondayNotAGoodDay/skills@eli5
npx skills add MondayNotAGoodDay/skills@poteto-mode
```

List without installing:

```bash
npx skills add MondayNotAGoodDay/skills --list
```

Browse on skills.sh: https://skills.sh/MondayNotAGoodDay/skills

## pstack

pstack is a set of rigorous engineering workflows: `poteto-mode` routes a task to a playbook, and the other skills (`how`, `why`, `arena`, `swarm`, `interrogate`, the `principle-*` skills) run as the steps need them. Read the [guide](docs/guide/README.md).

Upstream is written for Cursor. This fork runs on any harness (opencode, Devin, Cursor, Claude Code, and others) with one setup step.

### Setup

Run once per harness:

```text
/setup-pstack
```

Skills never name a harness tool or a model. They say "spawn role X with read-only access" and "ask the user". `/setup-pstack` finds out how your harness does those things by reading its own tool schemas, picks a model per role and a reasoning budget, and writes a short **pstack profile** into the harness's standing instructions. Where a harness binds the model to an agent definition instead of the spawn call (Devin does), it generates the agent files. It then spawns real subagents with the finished recipe to prove it works.

Without a profile, skills still run. They spawn a general-purpose subagent with no model override, or run roles one after another if the harness cannot spawn.

### Entry points

| Skill | When to use |
| --- | --- |
| [setup-pstack](skills/setup-pstack/SKILL.md) | Adapt pstack to this harness. Run first. |
| [poteto-mode](skills/poteto-mode/SKILL.md) | Default entry for any task that needs rigor. |
| [poteto-help](skills/poteto-help/SKILL.md) | New to pstack, or unsure which skill fits. |
| [how](skills/how/SKILL.md) / [why](skills/why/SKILL.md) | How a subsystem works / why it was built that way. |
| [architect](skills/architect/SKILL.md) / [arena](skills/arena/SKILL.md) / [swarm](skills/swarm/SKILL.md) | Settle a shape / N parallel attempts / N parallel workers. |
| [interrogate](skills/interrogate/SKILL.md) | Multi-reviewer review of a branch or PR. |

The rest (`blast-radius`, `recall`, `reflect`, `tdd`, `unslop`, `no-comments`, the 24 `principle-*` skills, and others) are in [skills/](skills/).

### What differs from upstream

- Spawn blocks use `role` / `access` / `run` instead of Cursor's `Task` parameters. Model slugs are gone from every skill. Roles and their model classes live in [setup-pstack/references/roles.md](skills/setup-pstack/references/roles.md).
- `poteto-agent` and Comment Sicko are prompt files ([poteto-agent](skills/poteto-mode/references/poteto-agent.md), [comment-sicko](skills/no-comments/references/comment-sicko.md)), not Cursor agent definitions.
- Transcript, loop, and question tools are capabilities in the profile, not named tools.
- Not ported: `make-bot-ui` and the Benny automation pack. Both depend on Cursor Automations.

[UPSTREAM.md](UPSTREAM.md) records the upstream commit. The first commit after it is the verbatim import, so `git diff` shows exactly what changed.

## Other skills

| Skill | When to use |
| --- | --- |
| [eli5](skills/eli5/SKILL.md) | Dead-simple picture explainer (`/eli5 <topic>`) |
| [show-me](skills/show-me/SKILL.md) | Visual diagrams / code-shape sketches for the current topic |

## Layout

```text
skills/
  <skill-name>/
    SKILL.md
skills.sh.json     repo page groups on skills.sh
docs/guide/        pstack guide
third_party/       upstream license
```

Each `SKILL.md` has YAML frontmatter with `name` (equal to the directory name) and `description`.

## Groups on skills.sh

[skills.sh.json](skills.sh.json) owns the sections on the [repo page](https://skills.sh/MondayNotAGoodDay/skills). It is display-only: `npx skills add` reads the skill directories, not this file, so grouping never changes an install command or a skill's name. Every skill is listed exactly once, and a name in two groups belongs to the first one.

The other grouping levers, none of which are in use here, are the CLI's category walk (`skills/<category>/<name>/SKILL.md`, up to two category levels), the hidden buckets `skills/.curated/`, `skills/.experimental/`, and `skills/.system/`, `metadata.internal: true` in frontmatter, and [packs](https://skills.sh/docs/packs) for bundling skills across repos.

## License

MIT. pstack is derived from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack), Copyright (c) 2026 Lauren Tan, MIT, see [third_party/pstack/LICENSE](third_party/pstack/LICENSE). `eli5` adapted from [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community); `show-me` from [humanlayer/skills](https://github.com/humanlayer/skills).
