# Profile template

Fill the placeholders. Keep the whole block under 45 lines. Delete a role line to fall back to `inherit`. The block is plain text so any harness can load it as standing instructions.

The first three comment lines define the vocabulary for every pstack skill. Keep them.

````markdown
<!-- pstack:begin v1 -->
<!-- pstack profile. Written by /setup-pstack. Re-run it to change anything here. -->
<!-- Skills say: spawn <role> with access <read-only|full>, run <background|foreground>, isolation <local|cloud>, persona, resume. A role's value is a model id, or `inherit` for no model override. -->
<!-- Skills say: ask, history, loop, models. Each is a capability below. `unsupported` means use its fallback. -->
harness: {name and version if known}
budget: {unlimited|large|medium|small} ({effort})

## capabilities
spawn: {the exact call. Tool name, then each parameter and the value to pass for prompt, model, access, background, resume. Say where the model goes (per call, or fixed by the named agent)}
spawn.read-only: {how to get a read-only subagent, or `unsupported`}
spawn.background: {parameter or behavior, or `unsupported` (run foreground)}
spawn.resume: {how to continue a finished subagent, or `unsupported`}
spawn.nested: {yes|no. Whether a subagent may spawn subagents}
spawn.isolation.cloud: {how to run a subagent in a separate cloud or remote environment, or `unsupported`. Fallback: a separate local git worktree}
spawn.agents: {names of agent files this setup generated, or `none`}
ask: {the tool that asks the user a question, or `unsupported` (ask in the reply and stop)}
history: {the command or path that reads THIS session's transcript, verified, or `unsupported`. Fallback: the user pastes it}
loop: {how to re-run a prompt on a timer or until a condition, or `unsupported`. Fallback: one pass per user message}
skills: {the skill-load tool or directory convention}
models: {the command or tool that lists models, or `ask the user`}
effort: {how reasoning effort is expressed, or `none`}

## roles
{one line per role from references/roles.md, in that order}
<!-- pstack:end -->
````

## Fallback when no profile is in context

A pstack skill that finds no profile does this and tells the user once that `/setup-pstack` was not run.

- Spawn a general-purpose subagent with no model override, using whatever spawn tool exists.
- No spawn tool: run each role yourself, one after another, in a fresh pass that reads only that role's prompt file. Say that the fan-out was serial and same-model.
- Ask with the question tool if there is one, otherwise ask in the reply.
- Read history only from a path the user gives.

## Role value grammar

- `inherit`: the role runs on the parent's model. No override.
- `<model id>`: exactly as the harness spells it, effort included if the harness spells it that way.
- `<id>, <id>, ...`: a panel. One subagent per entry. An `inherit` entry counts toward the fan-out.
