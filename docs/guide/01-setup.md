# Set up pstack

In this page you install pstack, let it adapt to your harness, and run your first task. Setup is one install command plus a short conversation.

## Install the skills

From your project, or anywhere for a user-level install, run:

```bash
npx skills add MondayNotAGoodDay/skills
```

That installs every skill in the repo. See the [README](../../README.md) to install a single skill or list what's available.

## Adapt pstack to your harness

Run:

```text
/setup-pstack
```

pstack skills never name a harness tool or a model. They say "spawn this role" and "ask the user". [`/setup-pstack`](../../skills/setup-pstack/SKILL.md) turns those words into your harness's real calls. It does this in order:

1. It detects the harness from the tool schemas in the session.
2. It works out how the harness spawns subagents, asks you a question, reads this session's transcript, and loops.
3. It asks for a reasoning budget, then lists the models available to you and picks one per role (code delegates, judgment, the review panels). It shows you the table and asks what you want to change.
4. If the harness binds a model to an agent definition instead of taking one per spawn, it generates the agent files it needs.
5. It writes the pstack profile into your standing instructions.
6. It smoke-tests the recipe. It spawns a read-only subagent, spawns a background subagent that writes a file, and reads the transcript back.

The profile is a short block of plain text, under 45 lines, that your harness loads every session. It records the budget, the exact way to spawn, ask, read history, and loop in your harness (or `unsupported` with a fallback), and one line per role naming a model. Every pstack skill reads it.

The budget sets reasoning effort for every model in the table. `large` is `xhigh` and matches the defaults. `unlimited` is `max`, `medium` is `high`, and `small` is `medium`. Where the harness can't control effort, the budget only steers which model gets picked.

You only override what you care about. Delete a role's line to make it `inherit`. A rerun of `/setup-pstack` keeps any role you changed by hand. After a harness upgrade, or if a smoke test fails, run it again.

You might be wondering what happens if you only have one model. Then every role is `inherit` and the panels are a single entry, so panel reviews won't be model-diverse. The value `inherit` means the subagent runs on your parent chat's model, with no override. For a panel role the value is a list, and one subagent runs per entry, so the list length sets the panel size. Setup also configures `swarm workers`, the default model for every `/swarm` worker unless a race names a model for each arm.

## Accept the verification offer, or don't

At the end of setup, `/setup-pstack` looks for a way to prove app behavior in your project, either a `verify-*` skill or an existing harness. If it finds neither, it offers once to generate one with [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md).

Say yes and it writes `.agents/skills/verify-<app>/`, a project-local skill that teaches agents to drive your app the way a user does. It proves the skill works once before handing it over. Say no and setup moves on. You can run `/create-verification-skill` yourself any time. [Verify and ship](./06-verify-and-ship.md#create-a-project-verification-skill) covers it in depth.

If you're new to pstack, say yes. An agent that can check its own work keeps going until the check passes. An agent that can't hands every result back to you to check by hand. Of everything in this guide, the verification skill pays off the most.

After setup, start a new chat. The profile applies to new sessions.

## Keep the cost in check

pstack spends extra tokens on subagents and review panels. That's the price of the rigor. To spend fewer:

- Rerun `/setup-pstack` and pick a smaller reasoning budget or cheaper models. A strong model in the main chat with cheaper, faster models in the code roles is a good split.
- Set a role to `inherit` so it runs on the chat's own model.
- Shorten a panel list. Each entry runs one subagent.
- Save `/poteto-mode` for work that needs rigor. A small, obvious edit doesn't.

## Run your first task

Pick something real but small, and describe it the way you'd describe it to a colleague:

```text
/poteto-mode add a --json flag to this command. text output stays byte-identical. verify both.
```

Watch the todo list. Its first items are the matched playbook's steps copied in, the Feature playbook for this prompt. If `/poteto-mode` skips a step, the step stays in the list with `skip: <reason>`, so you can see what it chose not to do.

From here you can type normal follow-ups. To keep `/poteto-mode` on for the whole chat, pin it if your harness can keep a skill in context on every turn. Otherwise start each new task with `/poteto-mode`. A skill invoked once attaches to one message, and it fades as the chat moves on.

Next: [Route work through `/poteto-mode`](./02-poteto-mode.md).
