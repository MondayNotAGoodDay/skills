# Roles

Skills refer to these exact role names. Setup writes one line per role into the profile, in this order. Ordinary skill prose never names a model. This file is the only place that records a default model id.

When the harness lists the default id, use it. Otherwise use the same family's available model at the highest effort at or below the role's default effort. If that family is missing, mark the role as needing a choice. `inherit` is the fallback when the user has no model to confirm.

## Families and vendors

Families are the ones Phase A of the **arena** skill defines. Fable (`claude-fable`) is its own family, apart from the other Claude models. Fable and those other Claude models are one vendor. That vendor split is what `arena cross-judge pool` uses, and it is why that pool does not include Fable.

Gemini tops out at `high`. Fable 5.2 and Gemini have no fast variant, so never write one for them. Opus 5.5 always runs its `-fast` slug. The effort token is the token before that `-fast`.

No role runs above `xhigh`. Outside a panel, Fable 5.2 runs only when the user names it for the task, or after two Opus 5.5 runs at `xhigh` of the same brief fail their check. Difficulty alone never qualifies. The `hardest tasks` role is retired.

## Classes

- **code**: Opus 5.5 when the harness offers it, always the fast variant. Default effort `xhigh`, except `swarm workers`, whose default effort is `high`.
- **judgment**: Opus 5.5 at `xhigh`, fast variant, for prose and review.
- **panel**: three seats when the harness can fill them, in this order: Fable 5.2 at `high`, Opus 5.5 at `high` fast, Gemini 4 Carbon at `high`. `arena cross-judge pool` drops the Fable seat. Drop a seat whose family is missing. A panel role may instead be `ask`, so the skill has the user pick models each run.

When the harness offers one model, every role is `inherit`.

## Table

| Role line | Class | Default | Default effort | Used by |
| --- | --- | --- | --- | --- |
| `feature, refactoring` | code | `claude-opus-5-5-xhigh-fast` | xhigh, fast | poteto-mode feature and refactoring playbooks, code delegates |
| `bug-fix` | code | `claude-opus-5-5-xhigh-fast` | xhigh, fast | poteto-mode bug-fix playbook |
| `perf-issue` | code | `claude-opus-5-5-xhigh-fast` | xhigh, fast | poteto-mode perf-issue playbook |
| `hillclimb` | code | `claude-opus-5-5-xhigh-fast` | xhigh, fast | poteto-mode hillclimb playbook |
| `judgment and prose` | judgment | `claude-opus-5-5-xhigh-fast` | xhigh, fast | poteto-mode delegates that write prose or judge |
| `arena runners` | panel | `claude-fable-5-2-high, claude-opus-5-5-high-fast, gemini-4-carbon-high` | high | arena |
| `arena cross-judge pool` | panel | `claude-opus-5-5-high-fast, gemini-4-carbon-high` | high | arena. Arena runs one judge. It tries only entries from a vendor other than the parent's, in list order, so order the list by preference. An `inherit` entry never judges |
| `swarm workers` | code | `claude-opus-5-5-high-fast` | high, fast | swarm. The default for every worker unless a race assigns a model per arm. Trivial mechanical edits and verification read this line |
| `interrogate reviewers` | panel | `claude-fable-5-2-high, claude-opus-5-5-high-fast, gemini-4-carbon-high` | high | interrogate |

## Not a profile role

These ids are not profile lines. The budget does not move them.

- **Eval dispatch.** An agent that only dispatches, polls, or packs results runs on `claude-opus-5-5-medium-fast`.
- **poteto-agent.** The persona pins `claude-opus-5-5-xhigh-fast`. A role valued `inherit` spawns a general-purpose subagent with no persona and no model override. Using the persona would apply this pin.

## Budget

Each role has a default effort in the table. The budget moves that effort by steps on the ladder `xhigh` > `high` > `medium` > `low`. No role runs above `xhigh`.

| Budget | Step | Where an `xhigh` default lands |
| --- | --- | --- |
| `large` | keep the default | xhigh |
| `medium` | lower one step | high |
| `small` | lower two steps | medium |

`large` equals the defaults. A target at the bottom of the ladder stays there. Apply the role's target to every model in that role, panel entries included. The effort token is the last token of the model id, or the token before a trailing `fast`. When the exact level is missing for a model, use the highest level at or below the target. When the harness has no effort control, ignore the target for the value and let it steer model choice only.

Gemini tops out at `high`, so a higher target stays at `high`. Fable 5.2 and Gemini have no fast variant. Opus 5.5 always keeps `-fast`.

`inherit` and `ask` never change with the budget.

Panel seats keep the level this budget wrote. Raising a role one step for the hardest work, up to `xhigh`, does not apply to a panel seat.
