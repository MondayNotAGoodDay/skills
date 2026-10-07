# Harness hints

Leads to check against this session's tool schemas. Each entry says how it was learned. A hint that the live schemas contradict is wrong. Fix this file when you find one.

## opencode

Source: this repo's author's session and the opencode skill docs.

- Spawn: tool `subagent` with `agent`, `prompt`, `model` (`provider/model` or `provider/model#variant`), `background`, `sessionID` (resume).
- Built-in agents: `general` (full access), `explore` (read-only). Read-only for a custom role needs an agent that denies edit and shell.
- Models bind per call. No generated agent files are needed for model choice.
- Model list: the `models` tool, reachable through the code-mode `execute` tool as `tools.opencode.models`. Variants carry the effort.
- Ask: tool `question`.
- Standing instructions: `~/.config/opencode/AGENTS.md`.
- Custom agents: `~/.config/opencode/agents/<name>.md`. Frontmatter takes `description`, `mode`, `model`, `permissions`.
- Nested spawn: allowed unless the agent's permissions deny `subagent`.
- Cloud isolation: none. Use a local git worktree.
- History: verify by probing. `opencode export` and `opencode session list` are leads.

## Devin CLI

Source: docs.devin.ai/cli (subagents, configuration, skills), read 2026-10.

- Spawn: tools `run_subagent` and `read_subagent`. Foreground or background.
- Built-in profiles: `subagent_explore` (read-only, runs on the default subagent model) and `subagent_general` (full tools, runs on the parent's model).
- **Model for a write-capable subagent is set only by the agent definition file.** There is no per-call model. This is the case that needs step 6 of setup.
- Custom agents: `~/.config/devin/agents/<name>.md` or `<name>/AGENT.md`. Frontmatter: `name`, `description`, `model`, `allowed-tools`, `max-nesting`.
- Nested spawn: off by default. A custom profile opts in with `max-nesting`.
- Standing instructions: `~/.config/devin/AGENTS.md`.
- Skills: `~/.agents/skills/` or `~/.config/devin/skills/`.
- Subagents can be disabled with `subagents_enabled: false` or by an org policy. In that case `spawn` is `unsupported`.
- Ask, history, loop: not confirmed. Probe.

## Cursor

Source: the upstream pstack plugin (cursor/plugins), which was written for it.

- Spawn: tool `Task` with `subagent_type`, `model`, `readonly`, `run_in_background`, `environment` (`cloud` or `local`), resume by agent id.
- Models bind per call. Ids look like `<family>-<version>-<effort>`.
- Ask: `AskQuestion`.
- Standing instructions: `~/.cursor/rules/*.mdc` with `alwaysApply: true`.
- Skills: `~/.cursor/skills/`.
- History: `~/.cursor/projects/<slug>/agent-transcripts/`, where `<slug>` is the workspace path with the leading slash dropped and each `/` turned into `-`. The system prompt names the active directory.
- Loop: built-in `/loop`.

## Claude Code

Source: not verified in this repo. Check every item.

- Spawn: a subagent tool (`Agent` or `Task`) with a `subagent_type` and possibly `model`.
- Standing instructions: `~/.claude/CLAUDE.md`.
- Custom agents: `~/.claude/agents/<name>.md`.
- Skills: `~/.claude/skills/`.

## Anything else

Derive every row from the schemas. If the harness has no standing-instructions file, use the project `AGENTS.md` with the user's consent, and fall back to `~/.config/pstack/profile.md`.
