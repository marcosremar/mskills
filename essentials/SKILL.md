---
name: mskills-essentials
description: Use when the user says "what's still missing", "what's essential", "implement everything", "implement everything that's missing", "I tested everything", "o que ainda falta", "implementa tudo", "implementa tudo que faltava", "testou tudo", or is about to implement a pile of missing work with no proof of what the app must do and whether that path works. Invoke with /mskills-essentials.
trigger: /mskills-essentials
---

# Essentials

"Implement everything that's missing and test everything" is not permission to implement. Prove the essential path and plan only what is absent or broken.

Start by saying you are using mskills-essentials and that this pass does not edit code.

## When to use

- "what's still missing", "implement everything", "I tested everything"
- "o que ainda falta", "implementa tudo", "testou tudo"
- The next step would be opening the editor on a list of improvements

Do not use for a bug the user already named (`bug-fix`), dead-weight cleanup (`mvp-bouncer`), a broad audit (`improve`), or a plan that already exists (`executing-plans`).

## Essential

Essential is what a person must finish for the app to do its job. Source, in this order:

1. The flow named in the message
2. The promise in `CLAUDE.md`, `AGENTS.md`, or the README
3. If none of those exist: open the app, do the job, get the result

More than 7 items means the list is wide. Cut until the path that finishes the job remains.

Leave out polish, refactors, "would be nicer", features off this path, dead code, and performance work that is not breaking an essential path.

## Steps

1. Write the essential list before reading the implementation. One line per behavior, from the user's point of view.
2. Test each item. A test is a run: an existing spec, the project's command, or the path in the browser. Finding the function in code does not count as working.
3. State: works, broken, absent, or not tested. "Works" requires what ran and what happened. Without a run, the state is "not tested".
4. The plan takes only broken or absent rows. "Not tested" stays in a separate sentence, with the command that was not run. It does not become an implementation task.

## Plan

| Essential | State | Proof | Smallest fix |
|---|---|---|---|

Each fix is the smallest change that makes that item pass, plus the command that proves it. Order: what blocks the path first.

If everything that was tested works, say so and stop. Do not invent work.

## Stop

Do not edit code, commit, or fix something else while you are here. Implementation starts only after the person sees the table and tells you to implement that list.

Do not write "I implemented everything that was missing" or "I tested everything". Name the item and the proof, or say you did not run it.
