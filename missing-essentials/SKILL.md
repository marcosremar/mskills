---
name: mskills-missing-essentials
description: Use when the user says "implement missing essentials", "missing essentials for this project", "missing essentials for this feature", "implement everything that's missing", "o que ainda falta", "o que falta de essencial", "implementa tudo que faltava", or names a project or a feature whose essential behavior may be missing or broken. Invoke with /mskills-missing-essentials.
trigger: /mskills-missing-essentials
---

# Implement missing essentials

Implement only the essentials that are missing or broken. The scope is the whole project, or one feature named in the message. Nothing else.

Start by saying you are using mskills-missing-essentials and the scope: `project`, or the feature name.

## When to use

- "implement missing essentials", "for this project", "for this feature"
- "o que ainda falta", "o que falta de essencial", "implementa tudo que faltava"
- A request to implement a pile of missing work

Do not use for a bug already named (`bug-fix`), dead-weight cleanup (`mvp-bouncer`), a broad audit (`improve`), or a plan that already exists (`executing-plans`).

## Scope

One pass, one scope.

- A named feature is the whole scope. Do not survey the rest of the app.
- No feature named: the scope is the project, meaning the promise in `CLAUDE.md`, `AGENTS.md`, or the README.

## Essential

Essential is what a person must finish for that scope to do its job. Write the list before reading the implementation. One line per behavior, from the user's side.

More than 7 items means the list is wide. Cut until the path that finishes the job remains.

Leave out polish, refactors, "would be nicer", other features, dead code, and performance that is not breaking this scope.

## Prove, then implement

1. Test each item. A test is a run: an existing spec, the project's command, or the path in the browser. Finding the function in code does not count as working.
2. State: works, broken, absent, or not tested. "Works" needs what ran and what happened. Without a run, the state is "not tested".
3. Implement only broken and absent rows. The smallest change that makes that row pass. Order: what blocks the path first.
4. Re-run the proof for each row you changed.
5. "Not tested" is not work to implement. Name the command you could not run and leave that row.

If every tested essential works, implement nothing.

## Report

| Essential | State | Proof | Change |
|---|---|---|---|

Do not write "I implemented everything that was missing" or "I tested everything". Name the item and the proof, or say you did not run it.

Do not commit unless asked. Do not edit anything outside the rows you are fixing.
