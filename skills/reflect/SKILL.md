---
name: reflect
description: Spawn three parallel review subagents over the active transcript, surface learnings, and route each to a concrete edit on an existing skill. Use when the user says reflect.
disable-model-invocation: true
---

# Reflect

Mine the current conversation for durable learnings, then route them into skill edits.

## When to invoke

Invoke when the user says "reflect" or "/reflect". Skip when the conversation is trivial, off-topic, or already covered by an existing skill the parent followed correctly. One-offs are not learnings.

## Process

### 1. Locate the active transcript

The parent finds its own transcript before fanning out. Read this session's transcript with the profile's `history` recipe. Use only the transcripts the `history` recipe can reach. Do not search other workspaces or projects. That crosses workspace boundaries and reads private chats from unrelated projects.

If the recipe reaches more than one transcript, check each candidate's opening user prompt against the conversation's opening user prompt. Take the matching one. If no transcript resolves, write a tight digest of the session and pass that instead. If the profile says `history: unsupported`, ask the user to paste the transcript, and note that without it the reviewers work from a digest only.

### 2. Spawn three reviewers in parallel

One message, three spawns, each with `access`: `full` and `run`: `foreground`. Reviewers need MCP access for context lookups (tickets, chat threads, observability traces referenced in the transcript). A read-only subagent may lose MCP tools in some harnesses, and these roles need them. Reviewers must not modify files.

Each spawn below names a `role` from the pstack profile (written by `/setup-pstack`, always in your context). Spawn it with the profile's `spawn` recipe, passing that role's value as the model. A role with no line, or the value `inherit`, means no model override. If the harness rejects the model, retry with `inherit` and say so. With no profile in context, follow the fallback in the `setup-pstack` skill (`references/profile-template.md`) and tell the user once that `/setup-pstack` was not run.

| Lens | `role` | Prompt template |
|---|---|---|
| Judgment | `reflect judgment, divergent, synthesizer` | `references/judgment-reviewer.md` |
| Tooling | `reflect tooling` | `references/tooling-reviewer.md` |
| Divergent | `reflect judgment, divergent, synthesizer` | `references/divergent-reviewer.md` |

Pass each template verbatim, substituting the transcript path or digest where marked. Reviewers return findings in the spawn response.

### 3. Synthesize

Spawn one synthesizer with `role`: `reflect judgment, divergent, synthesizer`, `access`: `full`, `run`: `foreground`. The synthesizer's quality check includes spot-verifying citations, which can require MCP access. A read-only subagent may lose MCP tools in some harnesses. The synthesizer must not modify files. Use `references/synthesizer.md` verbatim, with each reviewer's full output inlined where marked. The synthesizer returns a structured Accepted / Rejected / Backlog list.

### 4. Structural enforcement check

Sanity-check the synthesizer's Accepted list. For any item that would be enforced more reliably by a lint rule, script, metadata flag, or runtime check, move it from Accepted to Backlog. See the **encode-lessons-in-structure** principle skill.

### 5. Apply

Before applying any Accepted edit, present the synthesizer's full Accepted/Rejected/Backlog output to the user and wait for explicit approval. The user picks which subset to apply and may redirect routings. Skill changes affect every future agent in the org. Do not auto-apply.

Backlog items file to whatever devex / backlog tracker your team uses automatically. Only the Accepted list waits for approval.

For each approved Accepted item, follow the Routing field exactly:

- Trivial existing-skill edit (a one-line bullet, a tightened sentence, a stale fact corrected): parent does directly.
- Substantive existing-skill edit (a new section, a new pattern table, more than ~10 lines): hand to the `create-skill` skill, if one is installed, and run its draft / test / iterate loop.
- `tune description: <skill path>` (the skill exists but didn't trigger when it should have): hand to `create-skill` and run its description-optimization loop.
- `new skill via create-skill: <kebab-name>`: hand creation to `create-skill`. Do not invent the shape ad hoc.

If your environment ships a SKILL.md validator, run it on every touched skill before declaring done. Skip this step if it doesn't.

### 6. Summarize for the user

Short list, no preamble:

- Edits applied: `<skill path>`. What changed, one line each.
- New skills created: `<skill path>`. One line each (rare).
- Backlog filed to the devex tracker: `<issue title>` (`<tags>`). One line each.
- Dropped: one line per rejected finding + reason from the synthesizer.
