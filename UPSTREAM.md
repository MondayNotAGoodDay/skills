# Upstream

## pstack

Forked from [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack) `pstack/` (MIT, Lauren Tan / Cursor).

- Version: 0.15.15
- Commit: d0ef80d86795816da932a153458c5dbe192d294e (2026-10-06)

The first commit after this file lands is the verbatim import. Every later commit is the harness-neutral rewrite, so `git diff <import>..HEAD` shows exactly what changed.

Not imported: `automations/benny` (depends on Cursor Automations), `assets/logo.png`.

## html-plan

Imported from [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community/tree/main/html-plan) `html-plan/skills/html-plan/`.

- Commit: f60f0454df3045f724c43c6346ec80bdcc3472b2 (2026-10-05)
- License: MIT, Thariq Shihipar. That is what the plugin's own `.claude-plugin/plugin.json` declares; the mirror repo's root LICENSE is Apache-2.0 and disagrees. The plugin declaration is the closer statement about this code, so MIT is what we follow. See [third_party/html-plan/LICENSE](third_party/html-plan/LICENSE).

Not imported: `.claude-plugin/plugin.json` and `html-plan/README.md` (both describe Claude Code plugin install, which skills.sh does not use).

Changed on import: `disable-model-invocation: true` and a narrowed `description`, so it does not compete with `architect` or `answer-me-with-html`; the `Artifact tool` bullet in SKILL.md rewritten for any harness; three reader-facing strings in `runtime/htmlplan.js` that said "Claude" made neutral. No runtime logic changed.

Upstream is a nightly-synced read-only mirror and was changing several times a week in October 2026, so this is a snapshot. To follow it later, diff `html-plan/skills/html-plan/` against the upstream path above.
