# Upstream

## pstack

Forked from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack) `pstack/` (MIT, Lauren Tan / Cursor).

- Version: 0.15.25
- Commit: 6ce45c59b9301a638c1fe1a7b8dc2a82b650898f (2026-10-14)

The verbatim import is `68f93b3`, pinned to upstream `d0ef80d` (0.15.15, 2026-10-07). The harness-neutral rewrite and the `ask` role were written on that import. Later releases were ported onto the fork: the prose follows upstream, and spawn blocks stay on the profile. 0.15.16 (distilled prompts, [#538](https://github.com/cursor/plugins/pull/538)) and 0.15.17 (`/ketchup`, [#539](https://github.com/cursor/plugins/pull/539)) landed first. 0.15.25 ([#614](https://github.com/cursor/plugins/pull/614)) rolls up the releases after that, through `/in-your-own-words`, `/sharpen`, the shorter visual `/ketchup`, and the playbook cuts that drop Graphite. Main also has [#633](https://github.com/cursor/plugins/pull/633) after this pin. That commit does not bump the version, so this fork stays on the 0.15.25 tree.

Not imported: `automations/benny` (depends on Cursor Automations), `assets/logo.png`.

## html-plan

Imported from [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community/tree/main/html-plan) `html-plan/skills/html-plan/`.

- Commit: f60f0454df3045f724c43c6346ec80bdcc3472b2 (2026-10-05)
- License: MIT, Thariq Shihipar. That is what the plugin's own `.claude-plugin/plugin.json` declares; the mirror repo's root LICENSE is Apache-2.0 and disagrees. The plugin declaration is the closer statement about this code, so MIT is what we follow. See [third_party/html-plan/LICENSE](third_party/html-plan/LICENSE).

Not imported: `.claude-plugin/plugin.json` and `html-plan/README.md` (both describe Claude Code plugin install, which skills.sh does not use).

Changed on import: `disable-model-invocation: true` and a narrowed `description`, so it does not compete with `architect` or `answer-me-with-html`; the `Artifact tool` bullet in SKILL.md rewritten for any harness; three reader-facing strings in `runtime/htmlplan.js` that said "Claude" made neutral. No runtime logic changed.

Upstream is a nightly-synced read-only mirror and was changing several times a week in October 2026, so this is a snapshot. To follow it later, diff `html-plan/skills/html-plan/` against the upstream path above.
