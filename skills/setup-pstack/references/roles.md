# Roles

Skills refer to these exact role names. Setup writes one line per role into the profile, in this order. Skills never name a model. This file is the only place that says what kind of model a role wants.

## Classes

- **code**: a strong, fast agentic coding model. Good at reading a codebase and making edits with tool calls. Cost and latency matter because these roles fan out.
- **judgment**: the strongest reasoning and writing model available. Used for synthesis, review, prose, and the hardest changes. Slow and costly is acceptable.
- **panel**: a list that makes independent attempts or reviews diverse. Take one `judgment` model and one `code` model from different model families when the harness offers two or more. Use `inherit` for a family you cannot confirm. Add a third family only when the user asks. A panel role may instead be `ask`, so the skill has the user pick models each run.

When the harness offers one model, every role is `inherit`. When it offers several, never choose the cheapest model for a `judgment` role.

## Table

| Role line | Class | Used by |
| --- | --- | --- |
| `feature, refactoring` | code | poteto-mode feature and refactoring playbooks, code delegates |
| `bug-fix` | code | poteto-mode bug-fix playbook |
| `perf-issue` | code | poteto-mode perf-issue playbook |
| `hillclimb` | code | poteto-mode hillclimb playbook |
| `judgment and prose` | judgment | poteto-mode delegates that write prose or judge |
| `hardest tasks` | judgment | cross-cutting design, gnarly concurrency, subtle algorithms |
| `how explorer` | code | how |
| `how explainer` | judgment | how |
| `why investigators` | code | why |
| `why synthesizer` | judgment | why |
| `reflect tooling` | code | reflect |
| `reflect judgment, divergent, synthesizer` | judgment | reflect |
| `arena runners` | panel | arena |
| `arena cross-judge pool` | panel | arena. Arena picks one entry whose family differs from the parent's when possible |
| `swarm workers` | code | swarm. The default for every worker unless a race assigns a model per arm |
| `architect runners` | panel | architect |
| `interrogate reviewers` | panel | interrogate |

## Budget

| Budget | Target effort |
| --- | --- |
| `unlimited` | max |
| `large` | xhigh |
| `medium` | high |
| `small` | medium |

Apply the target to every model in the table, panel entries included. Use the effort scale the harness exposes. When the exact level is missing for a model, use the highest level at or below the target. When the harness has no effort control, ignore the target for the value and let it steer model choice only.

`inherit` never changes with the budget.
