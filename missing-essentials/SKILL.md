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

## Walk, then prove

"All the code" is the hops from the entry the person opens to the result they get. Not the rest of the repo. Do these hops before the table. A row cannot be "works" until its hops are written.

1. Search the code for the entry (route, screen, or handler). Record `file:line`. A path taken from a doc is not an entry.
2. From that symbol, follow one direct call or import at a time, only while it still stands between the person and the result. Stop at the screen they see or the write they keep.
3. For each hop, read the lines that branch, write, or render. Record `file:symbol`, the line range, and what that hop does for the person. A filename from search, a graph, or a doc is not a hop.
4. Search the callers of the deciding function. Open a second caller that skips the behavior. It is another hop.
5. Run the test only after those lines are read. Match the assertion to the branch you read. A green spec whose assertion you did not match does not count.

These do not finish a hop: "the spec name matches", "the doc names the file", "the graph listed it", "I already know this code".

If a hop is unread, stop. No row is "works". Do not implement.

## Prove, then implement

State: works, broken, absent, or not tested. "Works" needs the line range, the command, and what happened. Without all three, the state is "not tested".

Implement only broken and absent rows, the smallest change that makes that row pass, blockers first. Re-run the proof for each row you changed. "Not tested" is not work to implement. If every tested essential works, implement nothing.

## Report

Write the hops before the table. One line each: `n. file:symbol lines a–b — what the person gets`. Then:

| Essential | State | Proof | Change |
|---|---|---|---|

Proof repeats the line range and the command. Do not write "I implemented everything that was missing", "I tested everything", or "I read the code". A report with no hop list is unfinished: go back to the walk.

Do not commit unless asked. Do not edit anything outside the rows you are fixing.
