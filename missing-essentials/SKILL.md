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

Essential is what a person must finish for that scope to do its job. The promise only suggests the list. The list is final after the code path below is walked. One line per behavior, from the user's side.

More than 7 items means the list is wide. Cut until the path that finishes the job remains.

Leave out polish, refactors, "would be nicer", other features, dead code, and performance that is not breaking this scope.

## Code of the scope

"All the code" means every file on the path from the entry the person opens to the result. Not the rest of the repo.

1. Name the entry (route, screen, or command) by finding it in the code. The doc is not the entry.
2. Walk imports and calls from that entry to the result. Write the file list before any row is "works".
3. Open every file on that list that branches, writes, or renders the essential. Read the branch, not the header.
4. A spec whose name sounds right is not that walk. A green spec does not count until the production file it claims to cover was opened and the assertion matches that branch.
5. If the walk is unfinished, stop. List the files not opened. No row is "works". Do not implement.

## Prove, then implement

1. Test each item only after its deciding file was opened. A test is a run: an existing spec, the project's command, or the path in the browser.
2. State: works, broken, absent, or not tested. "Works" needs the file and symbol that was read, plus what ran and what happened. Without both, the state is "not tested".
3. Implement only broken and absent rows. The smallest change that makes that row pass. Order: what blocks the path first.
4. Re-run the proof for each row you changed.
5. "Not tested" is not work to implement. Name the file not opened or the command not run, and leave that row.

If every tested essential works, implement nothing.

## Report

First line: `Path: <opened>/<on path>`. Then the files not opened, if any.

| Essential | State | Proof | Change |
|---|---|---|---|

Proof names `file:symbol` and the command. Do not write "I implemented everything that was missing", "I tested everything", or "I read the code". Name the item, the file, and the run.

Do not commit unless asked. Do not edit anything outside the rows you are fixing.
