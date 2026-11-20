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

pstack skills never name a harness tool or a model. They say "spawn this role" and "ask the user". [`/setup-pstack`](../../skills/pstack/setup-pstack/SKILL.md) turns those words into your harness's real calls. It does this in order:

1. It detects the harness from the tool schemas in the session.
2. It works out how the harness spawns subagents, asks you a question, reads this session's transcript, and loops.
3. It asks for a reasoning budget, then lists the models available to you and picks one per role (code delegates, prose, judgment, the review panels). It shows you the table and asks what you want to change. Out of the box, code, prose, and judgment prefer Opus 5.5 at xhigh, and a review panel prefers one seat each on Fable 5.2, Opus 5.5, and Gemini 4 Neon. Outside a panel, Fable 5.2 runs only when you name it for a task or after two Opus 5.5 runs of the same brief fail their check. When the harness does not offer one of them, setup picks the closest model of that family.
4. If the harness binds a model to an agent definition instead of taking one per spawn, it generates the agent files it needs.
5. It writes the pstack profile into your standing instructions.
6. It smoke-tests the recipe. It spawns a read-only subagent, spawns a background subagent that writes a file, and reads the transcript back.

The profile is a short block of plain text, under 45 lines, that your harness loads every session. It records the budget, the exact way to spawn, ask, read history, and loop in your harness (or `unsupported` with a fallback), and one line per role naming a model. Every pstack skill reads it.

The defaults are the same as the `large` budget. Code, prose, and judgment run on Opus 5.5 at `xhigh` reasoning. On a review panel, the Fable and Opus seats run at `high`, and the Gemini 4 Neon seat runs at `xhigh`. Swarm workers run at `high`. No role runs above `xhigh`. `medium` lowers each of those one level and `small` lowers it two, and both spend fewer tokens. The budget labels name where a default of `xhigh` lands, which is also where the Gemini 4 Neon panel seat lands. Where the harness can't control effort, the budget only steers which model gets picked.

You only override what you care about. Delete a role's line to make it `inherit`. A rerun of `/setup-pstack` keeps any role whose model differs from the default, and rebuilds the rest from the current defaults. The profile carries a `# defaults: 3` line. A profile without it is from an older setup, and its next run replaces the old defaults it recognizes, tells you which, and keeps your other choices. A saved panel seat on Gemini 4 Carbon, or on Gemini 4 Neon at `high`, is an old default even when the profile already has that line. The next run moves that seat to Gemini 4 Neon at `xhigh` and tells you. A Gemini 4 Neon seat at another level stays. To drop a pin it does not recognize, delete that role's line or the managed block first. After a harness upgrade, or if a smoke test fails, run it again.

You might be wondering what happens if you only have one model. Then every role is `inherit` and the panels are a single entry, so panel reviews won't be model-diverse. The value `inherit` means the subagent runs on your parent chat's model, with no override. For a panel role the value is a list, and one subagent runs per entry, so the list length sets the panel size. Setup also configures `swarm workers`, the default model for every `/swarm` worker unless a race names a model for each arm.

## Accept the verification offers, or don't

At the end of setup, `/setup-pstack` looks for a way to prove app behavior in your project, either a `verify-*` skill or an existing harness. If it finds neither, it offers once to generate one with [`/create-verification-skill`](../../skills/pstack/create-verification-skill/SKILL.md).

Say yes and it writes `.agents/skills/verify-<app>/`, a project-local skill that teaches agents to drive your app the way a user does. It proves the skill works once before handing it over. Say no and setup moves on. You can run `/create-verification-skill` yourself any time. [Verify and ship](./06-verify-and-ship.md#create-a-project-verification-skill) covers it in depth.

If you're new to pstack, say yes. An agent that can check its own work keeps going until the check passes. An agent that can't hands every result back to you to check by hand. Of everything in this guide, the verification skill pays off the most.

Then setup offers once to generate a formal verification skill with [`/create-formal-verification-skill`](../../skills/pstack/create-formal-verification-skill/SKILL.md), unless the project already has one. Tests check the cases someone thought to write. A formal verification skill checks one key property of your code, such as "a job never runs twice at once", in every case within a stated scope. Say yes when your code has concurrency, retries, a state machine, or a parser, where a bug is costly and tests rarely reach the case that breaks. Otherwise say no, and run it later if that changes. [Verify and ship](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill) covers it.

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

Watch the todo list. Its first items are the matched playbook's steps copied in, the Feature playbook for this prompt.

From here you can type normal follow-ups. To keep `/poteto-mode` on for the whole chat, pin it if your harness can keep a skill in context on every turn. Otherwise start each new task with `/poteto-mode`. A skill invoked once attaches to one message, and it fades as the chat moves on.

Next: [Route work through `/poteto-mode`](./02-poteto-mode.md).
