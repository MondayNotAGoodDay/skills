# Upstream

## pstack

Forked from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack) `pstack/` (MIT, Lauren Tan / Cursor).

- Version: 0.15.28
- Commit: d457567332aa6989e753ea289f30ea2bfbc80bc0 (2026-10-19)

The verbatim import is `68f93b3`, pinned to upstream `d0ef80d` (0.15.15, 2026-10-07). The harness-neutral rewrite and the `ask` role were written on that import. Later releases were ported onto the fork: the prose follows upstream, and spawn blocks stay on the profile. 0.15.16 (distilled prompts, [#538](https://github.com/cursor/plugins/pull/538)) and 0.15.17 (`/ketchup`, [#539](https://github.com/cursor/plugins/pull/539)) landed first. 0.15.25 ([#614](https://github.com/cursor/plugins/pull/614)) rolls up the releases after that, through `/in-your-own-words`, `/sharpen`, the shorter visual `/ketchup`, and the playbook cuts that drop Graphite. 0.15.27 ([#713](https://github.com/cursor/plugins/pull/713)) includes 0.15.26 ([#710](https://github.com/cursor/plugins/pull/710)) and the skill changes between them: restated principles ([#633](https://github.com/cursor/plugins/pull/633)), shorter skills ([#634](https://github.com/cursor/plugins/pull/634)), branded types without `as` ([#700](https://github.com/cursor/plugins/pull/700)), the docs refresh ([#704](https://github.com/cursor/plugins/pull/704)), `/ketchup` listing every waiting call ([#692](https://github.com/cursor/plugins/pull/692), [#708](https://github.com/cursor/plugins/pull/708)), `/create-formal-verification-skill` ([#709](https://github.com/cursor/plugins/pull/709)), and the `/sharpen` line that no longer refuses a rewritten prompt ([#712](https://github.com/cursor/plugins/pull/712)). [#715](https://github.com/cursor/plugins/pull/715) (`f9be7aa0`) does not bump the version. It offers formal verification from `/setup-pstack` and syncs help and the guide. 0.15.28 ([#724](https://github.com/cursor/plugins/pull/724), `d4575673`) is this pin. It includes the TLA+ default and the no-verifier fallback in `/create-formal-verification-skill` ([#721](https://github.com/cursor/plugins/pull/721)) and `/maintain-formal-verification-skill` ([#716](https://github.com/cursor/plugins/pull/716)). `/setup-pstack` does not offer the maintain skill, because upstream does not. Skill files stay at `skills/<name>/SKILL.md`.

Not imported: `automations/benny` (depends on Cursor Automations), `assets/logo.png`, `make-bot-ui`. Also not imported: the Claude Code install tree (`docs/harness/claude-code.md`, plugin manifests, and the generated harness copy). [#637](https://github.com/cursor/plugins/pull/637) and [#705](https://github.com/cursor/plugins/pull/705) only change that install path, and skills.sh does not use it. [#702](https://github.com/cursor/plugins/pull/702) renames `make-bot-ui` and `Poteto Mode`. This fork already names `poteto-mode` after its folder and does not ship `make-bot-ui`. The docs-vs-tree script from [#704](https://github.com/cursor/plugins/pull/704) lives outside `pstack/` and checks a generated Claude Code tree this fork does not build. [#724](https://github.com/cursor/plugins/pull/724) bumps `plugin.json` to 0.15.28 and regenerates that harness copy. This repo has no `plugin.json`. The harness copy's extra line is the Claude Code adapter note, which this fork does not ship. Zoom ([#717](https://github.com/cursor/plugins/pull/717)) and Meta Ads ([#720](https://github.com/cursor/plugins/pull/720)) landed on `cursor/plugins` main in the same range and live outside `pstack/`.

## html-plan

Imported from [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community/tree/main/html-plan) `html-plan/skills/html-plan/`.

- Commit: f60f0454df3045f724c43c6346ec80bdcc3472b2 (2026-10-05)
- License: MIT, Thariq Shihipar. That is what the plugin's own `.claude-plugin/plugin.json` declares; the mirror repo's root LICENSE is Apache-2.0 and disagrees. The plugin declaration is the closer statement about this code, so MIT is what we follow. See [third_party/html-plan/LICENSE](third_party/html-plan/LICENSE).

Not imported: `.claude-plugin/plugin.json` and `html-plan/README.md` (both describe Claude Code plugin install, which skills.sh does not use).

Changed on import: `disable-model-invocation: true` and a narrowed `description`, so it does not compete with `architect` or `answer-me-with-html`; the `Artifact tool` bullet in SKILL.md rewritten for any harness; three reader-facing strings in `runtime/htmlplan.js` that said "Claude" made neutral. No runtime logic changed.

Upstream is a nightly-synced read-only mirror and was changing several times a week in October 2026, so this is a snapshot. To follow it later, diff `html-plan/skills/html-plan/` against the upstream path above.
