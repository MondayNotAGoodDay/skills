---
name: setup-pstack
description: Adapt pstack to the harness you are running in. Detects the harness from this session's own tool schemas, discovers how it spawns subagents, asks the user, and reads history, then picks a model per role and a reasoning budget. Writes a pstack profile into the harness's standing instructions and generates agent files when the harness needs them. Use for /setup-pstack, "configure pstack", "pstack budget", "pstack models", or when a pstack skill says no profile is in context.
---

# Setup pstack

pstack skills never name a harness tool or a model. They say "spawn role X with access Y" and "ask the user". This skill turns those words into this harness's real calls by writing a **pstack profile** into the standing instructions the harness loads every session.

Run it once per harness. Re-run it to change the budget or models, after a harness upgrade, or when a smoke test fails.

## Rules

- **Evidence over memory.** Read tool names, parameters, and enums from this session's tool schemas. `references/harness-hints.md` is a list of leads to check, not facts to copy. A hint the schemas contradict loses.
- **Never write a model you did not confirm exists.** A harness with no model list means you ask the user to paste the ids.
- **Idempotent.** Re-runs replace the managed block and generated files, nothing else.
- **Prove it.** Step 8 spawns a real subagent with the finished recipe. A profile that was never exercised is a guess.

## Steps

### 1. Identify the harness

Name the harness from evidence, in this order.

1. The tool schemas in this session. Look for the tool that spawns a subagent, the one that asks the user a question, and the one that loads a skill.
2. The system prompt, which usually names the product.
3. Config directories that exist (`~/.config/opencode`, `~/.config/devin`, `~/.cursor`, `~/.claude`, `~/.codex`) and the env vars the product sets.

Compare against `references/harness-hints.md`. State the harness and the evidence in one sentence. If two harnesses fit or none does, ask the user. For an unknown harness, continue with step 2 anyway. Everything is derived from the schemas.

### 2. Discover capabilities

Fill every row of the capability table in `references/profile-template.md` from the schemas and from probing. Mark a row `unsupported` when the harness truly cannot do it. Do not leave a row blank.

Rows that need a real probe, not a guess:

- **How to read this session's transcript.** Find where the harness stores history and how to locate the current session's file or export. Run the command and confirm it returns this conversation. Several pstack skills (`recall`, `reflect`, `show-me-your-work`, `eval`, `session-pickup`) depend on it.
- **Models.** Use the harness's model list (a tool, a CLI, a config file). If none exists, ask for ids.
- **Effort control.** Whether reasoning effort is a model suffix, a variant, a parameter, or not controllable.
- **Where models bind.** Either the spawn call takes a model per call, or the model is fixed by the agent definition the call names. This decides step 6.

### 3. Load current state

Look for an existing managed block (`<!-- pstack:begin -->`) in the standing instructions file. Treat its `budget` and role values as the current choices. Also look for legacy forms and import their values, then remove them in step 7.

- Cursor: `~/.cursor/rules/pstack-models.mdc`.
- Any harness: a hand-written section titled `pstack subagent models`.
- WorkBuddy: a `## pstack model configuration` section in `~/.workbuddy/MEMORY.md`.

A role not in `references/roles.md` is from a retired role. Drop it and tell the user.

### 4. Budget

Ask for a budget. Name the current one when there is one. With none, say `large` matches the defaults.

- `unlimited` means max reasoning
- `large` means xhigh reasoning
- `medium` means high reasoning
- `small` means medium reasoning

The budget sets the target effort for every model in step 5. Where the harness cannot control effort, the budget only decides how strong a model to pick, and you say so.

### 5. Pick models

Build the role table from `references/roles.md`.

1. Classify the available models into `code`, `judgment`, and the families a `panel` needs, using the class definitions in that file.
2. Fill each role from its class. Apply the budget's target effort in the way step 2 found. If the exact effort is not offered, use the highest one at or below the target for that model.
3. If only one model is available, every role is `inherit` and panels are a single entry. Say that panels will not be model-diverse, and offer `ask` for the panel roles so the user picks models each time the skill runs.
4. Show the table. Mark any value you could not confirm. Ask whether to accept it or change specific roles. Offer the available models plus `inherit` (run this role on the parent's model).

On a re-run, keep every role the user changed by hand.

### 6. Generate agent files, only if the harness binds model to agent

If step 2 found that the spawn call takes a model per call, skip this step.

Otherwise each distinct (model, access) pair in the role table needs an agent definition the spawn call can name. Write one file per pair in the harness's agents directory, named `pstack-<model-slug>-<ro|rw>`. Use the harness's own frontmatter (from the schemas or docs, not from memory). `ro` limits tools to read and search. `rw` allows edit and shell. A persona prompt body is not needed here. Personas are passed as the task prompt.

Optionally also install `poteto-agent` natively, from `skills/poteto-mode/references/poteto-agent.md`, when the harness supports custom agents. Offer it once, skip on no.

Record the generated names in the profile's `agents` line so a re-run can clean them up.

### 7. Write the profile

1. Fill `references/profile-template.md` with the values from steps 2 to 6. Keep it under 45 lines. The block is in context every session.
2. Choose the target, in this order: the harness's user-level standing instructions file, then the project's `AGENTS.md` with the user's consent. If neither exists, write `~/.config/pstack/profile.md` and tell the user which file to include.
3. Replace the text between `<!-- pstack:begin -->` and `<!-- pstack:end -->`, or append the block. Remove legacy forms found in step 3. Touch nothing else in the file.
4. Show the user the final block and the file path.

### 8. Prove the recipe

Follow the profile you just wrote, literally, as a skill would.

1. Spawn one read-only subagent, foreground, on the `how explorer` role, with the prompt "List the files at the repository root. Reply with the list only."
2. Spawn one background subagent with `access: full` on a throwaway task that creates a file in the OS temp directory. Read the file back, then delete it.
3. Read this session's transcript with the profile's `history` recipe and confirm it contains this setup conversation.

On any failure, fix the recipe or the generated files and repeat. Report each probe as pass or fail. If a capability cannot work in this harness, set it to `unsupported` with the fallback from the template.

### 9. Report

Say what was written and where, that it applies to new sessions, and which capabilities are `unsupported` with their fallbacks. Re-running updates it.

### 10. Offer a verification skill (optional)

Check whether the project has a way to drive the real app for proof (a `verify-*` skill, or an existing harness). If not, offer once: "want a project-local verification skill, so agents can drive the app the way a user does and prove changes work? I can generate one with /create-verification-skill." On yes, run `create-verification-skill`. On no, move on.
