# skills

English | [中文](README.zh-CN.md)

Personal agent skills for the open [skills.sh](https://skills.sh) ecosystem. Includes **pstack**, a harness-neutral fork of [Cursor's pstack](https://github.com/cursor/plugins/tree/main/pstack).

## Install

```bash
# all skills in this repo (tracks main; skills update can pull new commits)
npx skills add MondayNotAGoodDay/skills

# one skill
npx skills add MondayNotAGoodDay/skills --skill eli5
npx skills add MondayNotAGoodDay/skills --skill poteto-mode

# pin a GitHub Release tag (skills.sh treats @vX.Y.Z as a git ref)
npx skills add MondayNotAGoodDay/skills@v0.1.1 --skill poteto-mode
```

List without installing:

```bash
npx skills add MondayNotAGoodDay/skills --list
```

Update installed skills:

```bash
npx skills check
npx skills update -y
```

Browse on skills.sh: https://skills.sh/MondayNotAGoodDay/skills

## Releases

Versions are Git tags in the form `vX.Y.Z`. Pushing such a tag runs [`.github/workflows/release.yml`](.github/workflows/release.yml) and publishes a GitHub Release that skills.sh can pin.

```bash
# from an up-to-date main
git tag -a v0.1.1 -m "v0.1.1"
git push origin v0.1.1
```

Use a later tag for the next release (`v0.1.2`, `v0.2.0`, …). Installs without a tag keep following `main`; pinned installs stay on that tag until the user adds a newer one.

## pstack

pstack is a set of rigorous engineering workflows: `poteto-mode` routes a task to a playbook, and the other skills (`why`, `arena`, `swarm`, `interrogate`, the `principle-*` skills) run as the steps need them. `/how` is there when you ask for it. Read the [guide](docs/guide/README.md).

Upstream is written for Cursor. This fork runs on any harness (opencode, Devin, Cursor, Claude Code, and others) with one setup step.

### Setup

Run once per harness:

```text
/setup-pstack
```

Skills never name a harness tool or a model. They say "spawn role X with read-only access" and "ask the user". `/setup-pstack` finds out how your harness does those things by reading its own tool schemas, picks a model per role and a reasoning budget, and writes a short **pstack profile** into the harness's standing instructions. Out of the box, code delegates, prose, and judgment prefer Opus 5.5 at xhigh, and no role runs above xhigh. Outside a review panel, Fable 5.2 runs only when you name it or after two Opus 5.5 runs of the same brief fail. A review panel prefers one seat each on Fable 5.2, Opus 5.5, and Gemini 4 Neon. `large` keeps those defaults. The profile carries a `# defaults: 3` line. A profile without it is from an older setup. The next run replaces the old defaults it recognizes and keeps your other choices. Where a harness binds the model to an agent definition instead of the spawn call (Devin does), it generates the agent files. It then spawns real subagents with the finished recipe to prove it works.

Without a profile, skills still run. They spawn a general-purpose subagent with no model override, or run roles one after another if the harness cannot spawn.

`/poteto-mode` matches one of twenty-two playbooks. Large, cross-cutting, or unattended work goes to `figure-it-out`. A started agent's kickoff points at [worker.md](skills/pstack/poteto-mode/worker.md). `/architect` sketches two shapes. `/reflect` reads the transcript itself and waits before it edits a skill.

### Entry points

| Skill | When to use |
| --- | --- |
| [setup-pstack](skills/pstack/setup-pstack/SKILL.md) | Adapt pstack to this harness. Run first. |
| [poteto-mode](skills/pstack/poteto-mode/SKILL.md) | Default entry for any task that needs rigor. |
| [poteto-help](skills/pstack/poteto-help/SKILL.md) | New to pstack, or unsure which skill fits. |
| [how](skills/pstack/how/SKILL.md) / [why](skills/pstack/why/SKILL.md) | How a subsystem works, from one read-only pass / why it was built that way, from the commits and PR discussions. |
| [architect](skills/pstack/architect/SKILL.md) / [arena](skills/pstack/arena/SKILL.md) / [swarm](skills/pstack/swarm/SKILL.md) | Sketch two shapes, and run arena when the design is large or contested / N parallel attempts / N parallel workers. |
| [interrogate](skills/pstack/interrogate/SKILL.md) | Adversaries try to break a branch or PR, and only findings a run proves survive. |
| [ketchup](skills/pstack/ketchup/SKILL.md) | Catch up on what changed in this chat since your last message, in plain words and small visuals, with every waiting call listed so you can decide from the reply alone. |
| [ketchup-video](skills/pstack/ketchup-video/SKILL.md) | The `/ketchup` catch-up as a short subtitled video of slides, with the calls waiting on you as text under it. |
| [in-your-own-words](skills/pstack/in-your-own-words/SKILL.md) | Play back the goal and the problem before the work starts. |
| [sharpen](skills/pstack/sharpen/SKILL.md) | Rewrite a prompt deeper, not bigger, before you send it. The rewrite asks the agent to prototype any choice an experiment can settle, and leaves product and preference choices to you. Doesn't do the task. |
| [create-formal-verification-skill](skills/pstack/create-formal-verification-skill/SKILL.md) | Prove one property tests rarely reach, and leave a project skill that rechecks it. |
| [maintain-formal-verification-skill](skills/pstack/maintain-formal-verification-skill/SKILL.md) | After covered code changes, bring the formal verification back in line and say whether a failure is drift or a bug. |
| [statechart](skills/pstack/statechart/SKILL.md) | Check a TLA+ model and draw what the user sees as a Mermaid statechart, mark each state that breaks a rule, and give the shortest path to each one. |

The rest (`blast-radius`, `recall`, `reflect`, `tdd`, `unslop`, `no-comments`, the 24 `principle-*` skills, and others) are in [skills/pstack/](skills/pstack/).

### What differs from upstream

- Spawn blocks use `role` / `access` / `run` instead of Cursor's `Task` parameters. Model ids stay in [setup-pstack/references/roles.md](skills/pstack/setup-pstack/references/roles.md). Ordinary skill prose does not name them.
- `poteto-agent` and Comment Sicko are prompt files ([poteto-agent](skills/pstack/poteto-mode/references/poteto-agent.md), [comment-sicko](skills/pstack/no-comments/references/comment-sicko.md)), not Cursor agent definitions.
- Transcript, loop, and question tools are capabilities in the profile, not named tools.
- Not ported: `make-bot-ui` and the Benny automation pack. Both depend on Cursor Automations.

[UPSTREAM.md](UPSTREAM.md) records the upstream commit this fork follows. Prompt prose tracks that commit. Spawn blocks stay on the profile.

## Other skills

| Skill | When to use |
| --- | --- |
| [eli5](skills/other/eli5/SKILL.md) | Dead-simple picture explainer (`/eli5 <topic>`) |
| [show-me](skills/other/show-me/SKILL.md) | Visual diagrams / code-shape sketches for the current topic |
| [html-plan](skills/other/html-plan/SKILL.md) | An implementation plan as one reviewable HTML page (`/html-plan <what to build>`) |

## Layout

```text
skills/
  pstack/          pstack workflow skills
    <skill-name>/
      SKILL.md
      references/  optional: long reference the skill reads on demand
      runtime/     optional: scripts the skill runs
      examples/    optional: a worked example
  other/           standalone skills (eli5, show-me, html-plan)
    <skill-name>/
      SKILL.md
skills.sh.json     groupings for the skills.sh repo page
docs/guide/        pstack guide
third_party/       upstream license
```

Each `SKILL.md` has YAML frontmatter with `name` (equal to the directory name) and `description`. Most skills also set `disable-model-invocation: true`, so they run only when named.

## License

MIT. pstack is derived from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack), Copyright (c) 2026 Lauren Tan, MIT, see [third_party/pstack/LICENSE](third_party/pstack/LICENSE). `html-plan` is from [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community/tree/main/html-plan), Copyright (c) 2026 Thariq Shihipar, MIT, see [third_party/html-plan/LICENSE](third_party/html-plan/LICENSE). `eli5` adapted from the same community repo; `show-me` from [humanlayer/skills](https://github.com/humanlayer/skills). [UPSTREAM.md](UPSTREAM.md) records every upstream commit.
