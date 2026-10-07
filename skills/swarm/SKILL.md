---
name: swarm
description: "Fan out N parallel workers, drain them, and return one report. Use for /swarm, 'swarm this', or parallel coverage, races, gauntlets, and exploration."
disable-model-invocation: true
---

# Swarm

Fan out N parallel cloud workers. They may cover separate slices, race the same brief, or mix both. The parent waits, aggregates, and returns one report.

Each spawn below names a `role` from the pstack profile (written by `/setup-pstack`, always in your context). Spawn it with the profile's `spawn` recipe, passing that role's value as the model. A role with no line, or the value `inherit`, means no model override. If the harness rejects the model, retry with `inherit` and say so. With no profile in context, follow the fallback in the `setup-pstack` skill (`references/profile-template.md`) and tell the user once that `/setup-pstack` was not run.

## Start

Open a todolist with one entry per phase before launching anything.

1. Frame
2. Fan out
3. Aggregate
4. Report

## Phase A: Frame

1. State the done predicate and the artifact or report the swarm must return.
2. Choose the shape. Partition into slices, race N workers on identical briefs, or mix both. For a race or mixed shape, declare `first pass`, `rank all`, or `best-of` before spawning.
3. Set N from the user or derive it from the shape. N is total workers, not the cloud concurrency limit.
4. Pick the worker model from the `swarm workers` role value. For `inherit`, pass no model override so the workers run on the parent model. For a model race, name each arm's model up front.
5. Give each worker its own writable output when it writes. When workers verify or measure commits, each brief names the exact SHAs. A measurement brief also names the method (sample count, what one sample is, order). The worker records both in its result.

## Phase B: Fan out

Spawn all N workers in one message with `isolation`: `cloud`, `run`: `background`, and the step 4 model, left unset for `inherit`. Use local isolation only when the worker needs access to something on the user's computer. If the profile says `spawn.isolation.cloud` is `unsupported`, give each worker its own local git worktree instead and say so.

When a worker must start from a non-default pushed branch, name that branch in the brief and pass it as the base branch when the profile's `spawn.isolation.cloud` recipe takes one.

Every brief stands alone. Include the goal, scope, exact slice or race arm, how to verify, and what to report. Reports use `PASS`, `ISSUES`, or `BLOCKED` with evidence. A worker that can prove a defect reports `ISSUES` and lists every issue it can prove, not only the first.

If a worker drops out, proceed with N-1 and note it.

## Phase C: Aggregate

Read the terminal results. Drop a result that does not record the SHAs and method its brief names, and respawn that worker once. After a second miss, record a gap. A gap does not count as a pass. For coverage, every required slice needs a result. For a race, apply the selection rule declared up front. Use first pass, rank all, or best-of. Do not paste raw worker dumps.

Keep a compact result table, one-line evidenced issues, and explicit gaps or dropouts.

## Phase D: Report

Return one consolidated in-chat report with the table, issue one-liners, gaps or dropouts, and the race rule when used.
