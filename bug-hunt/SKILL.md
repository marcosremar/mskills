---
name: mskills-bug-hunt
description: Use when the user asks to hunt for bugs, "caçar bugs", find bugs before a release, or check whether an app or a module is ready for production. Assesses risk through complexity, coverage and structural analysis, then spawns focused investigators that write a reproducing test for every suspected bug. Thoroughness over speed. Advisory: produces findings with reproducing tests; fixes only on the user's yes. Invoke with /mskills-bug-hunt.
trigger: /mskills-bug-hunt
---

# Bug hunt

Adapted from [chrisallenlane/claude-swe-workflows](https://github.com/chrisallenlane/claude-swe-workflows) ([`skills/bug-hunt` at commit `20f50d4a`](https://github.com/chrisallenlane/claude-swe-workflows/tree/20f50d4a/skills/bug-hunt), MIT — see `LICENSE` next to this file). Changes: project rules read from the repository, no tickets, no automatic commits, and the two agent definitions in `agents/` next to this file.

Start by saying you are using mskills-bug-hunt.

An assessor finds the high-risk hotspots by cross-referencing code complexity, test coverage gaps and structural risk. Focused hunters then dig into each hotspot and write a reproducing test for each suspected bug.

**Deliberately thorough.** Every suspected bug gets a reproducing test. No speculative reports: confirmed findings with evidence, not a list of maybes.

**Advisory.** The hunt produces findings; it does not fix them. Pressure to find bugs shouldn't bias the hunters toward bugs they could easily fix, and a fix needs fresh reasoning. The reproducing tests are the acceptance criteria: a fix is done when its test passes.

## The agents

The skill uses two agents, defined in `agents/` next to this file:

- `agents/swe-bug-assessor.md` — the risk assessor.
- `agents/swe-bug-hunter.md` — one focused hunter per hotspot.

If the agent types `swe-bug-assessor` / `swe-bug-hunter` are installed (copied into `~/.claude/agents/` or the project's `.claude/agents/`), spawn them. Otherwise spawn a general-purpose agent and start its prompt with: "Read `<path to this skill>/agents/swe-bug-<role>.md` first and follow it exactly: methodology, authority and output format."

## Project rules (override everything below)

Before spawning anything, read the project's own instructions — `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, the README's test section — and write down, for every agent:

- **How to build and run tests**, exactly as the project does it (its scripts, its package manager, any "one build at a time" rule, filters to run a single test). Run only what each hotspot needs; the full suite only if the user asks.
- **What tests may not cost**: never call production services, paid APIs or real hardware the project marks as such; use the project's fakes, mocks or local servers. If the project has no such rule, still never spend money or touch production.
- **Coverage tooling**: use it only if the project already runs it and it is cheap here; otherwise the assessor maps coverage by reading the tests.
- **Known issues** the project lists are not findings.
- **A confirmed-bug test must not break the suite.** After confirming it fails, mark it with the language's skip-with-reason mechanism, the reason starting with `bug-hunt:` — Rust `#[ignore = "bug-hunt: …"]` (run with `-- --ignored <name>`), pytest `@pytest.mark.xfail(strict=True, reason="bug-hunt: …")`, Jest/Vitest `test.failing(…)` / `it.fails(…)`, Go `t.Skip("bug-hunt: …")`, JUnit `@Disabled("bug-hunt: …")`. Coverage tests that pass stay unmarked.
- Test names follow the project's style.
- Talk to the user in the user's language.

Pass these rules to **every** agent you spawn.

## Workflow

```
1. Determine scope
2. Spawn the assessor (risk analysis) → ranked hotspot list + coverage map
3. For each chosen hotspot, one hunter at a time
   (prior findings passed to the next hunter)
4. Verify and synthesize the findings
5. Present the consolidated report
6. Propose fixes (no tickets); fix only on the user's yes
7. Commit only when the user asks
```

### 1. Determine scope

**Default:** production code only. Excluded by default:
- test code (test files, fixtures, helpers)
- dev-only tooling, scripts, benchmarks, training code
- generated and vendored code
- code the project marks as legacy or no longer used

Tell the user these exclusions, then ask:
- "What is the scope of the hunt?" (whole codebase, one module, or one area of concern)
- "Any areas you're worried about?" (recent changes, complex features)
- "Anything else to skip?"

User concerns shape the priorities but don't replace the systematic analysis. A hunt over a whole app can spawn 10–20 agents: say so.

### 2. Risk assessment

Spawn the assessor:

```
You are the risk assessor for a proactive bug hunt. Your analysis will guide
focused investigators who will deep-dive into the hotspots you identify.

Scope: [entire codebase | user-specified scope]
User concerns: [areas mentioned, or "none specified"]
Exclusions: [test code, vendored code, generated code, plus user additions]

Perform your full methodology:
1. Map the codebase — language, framework, structure, entry points
2. Coverage analysis — tooling if the project rules allow it, otherwise
   manual inspection of the existing tests
3. Complexity analysis — functions with high cognitive complexity
4. Structural risk analysis — error handling gaps, input validation gaps,
   shared mutable state, resource management, concurrency, edge cases,
   consistency gaps
5. Git enrichment (optional) — churn hotspots, recent large changes,
   files that keep appearing in fix commits
6. Cross-reference the signals and produce a ranked hotspot list

Focus on hotspots where MULTIPLE signals converge — complex AND untested AND
structurally risky. Single-signal hotspots are lower priority.

For each hotspot, say which test target can reproduce it and what it needs
(a model on disk, a database, a local server).

[project rules]
```

**When the assessor reports back:** show the user the hotspot list and its size before spawning hunters, and ask whether to investigate all of it or only the critical/high tiers.

### 3. Hunters

For each chosen hotspot, spawn a dedicated hunter:

```
You are a focused bug hunter investigating a specific hotspot.

## YOUR HOTSPOT
Target / Files / Risk signals / Hypothesis / Investigation approach /
Test target: [from the assessor's report]

## PRIOR FINDINGS
[confirmed bugs and patterns from previous hunters]

## YOUR MISSION
Deep-dive into this hotspot. The line numbers may be stale and the
hypothesis may be wrong: read the code first, and say so if it is wrong.
For each suspected issue, write a reproducing test that encodes the correct
expected behavior.

- If the test FAILS: bug confirmed. Keep it, mark it as the project rules say
  (skip-with-reason "bug-hunt: …") so the suite stays green, run it once more
  to confirm it fails for the reason you expect, and document the finding with
  the failure output.
- If the test PASSES: hypothesis invalidated. Keep the test only if it covers
  a previously untested path; otherwise delete it.
- No seam to reproduce it without changing production code: report it as
  SUSPECTED BUT UNCONFIRMED with the exact code path and reasoning.

Never change production code, never commit. List every file you changed and
every test you added.

[project rules]
```

**Run the hunters one at a time, never in parallel**: each hunter's findings and patterns go to the next one, and builds often can't run concurrently anyway. Keep the user's task list current: one step per hotspot. Tell the user each hunter's result as it arrives.

### 4. Verify and synthesize

**Verify before reporting:** run every confirmed-bug test once yourself and read its assertion. A hunter's report is not proof.

Then look across the findings:
- Do confirmed bugs share a root cause or pattern?
- Is there a systemic issue (a shared helper that is wrong everywhere, though only one place was a hotspot)?
- Do the coverage tests of invalidated hypotheses point at areas worth another look?

If several hunters report the same pattern, call it systemic even when each instance is minor.

### 5. Present the consolidated report

```
## Bug hunt summary

Scope: [what was analyzed]
Assessment: [N hotspots across X files]
Hotspots investigated: [N]
Confirmed bugs: N (X critical, Y high, Z medium, W low)
Coverage improvements: N tests added
Systemic patterns: N

## CONFIRMED BUGS

### CRITICAL
- **[file:line — description]**
  - Bug: [concrete description]
  - Root cause: [why it exists]
  - Impact: [what happens to the people using the app]
  - Reproducing test: [file:test name] (how to run it)
  - Fix guidance: [what needs to change]

### HIGH / MEDIUM / LOW
[same format]

## SYSTEMIC PATTERNS
- [pattern] — seen in [locations] — suggests [recommendation]

## COVERAGE IMPROVEMENTS
- [test name] in [file] — covers [what]

## SUSPECTED BUT UNCONFIRMED
- [description] — couldn't test because [reason]

## AREAS NOT INVESTIGATED
[deprioritized hotspots, out-of-scope areas worth a later look]
```

Walk through the critical findings first: the bug, the impact, the reproducing test. Let the user ask questions before moving on. If the report is long, offer it as a page the user can share.

### 6. Propose fixes (no tickets)

Propose one fix per critical/high bug, one per systemic pattern, medium/low batched. Each fix is done when its reproducing test passes without the `bug-hunt:` skip marker. Ask which to fix first; fix only on the user's yes.

### 7. Commit (only when asked)

Never commit on your own. Leave the test files uncommitted and list them. If the user asks for a commit: branch first if on the default branch, put the reproducing tests in one commit whose message names the bug hunt.

## Coordination

- The assessor runs first, then the hunters one at a time.
- No remediation agents inside the hunt.
- A fresh agent for every role, with a clean context.
- Keep as orchestrator: the hotspot list and coverage map, every hunter's findings (passed on), running totals, every test file created or changed, progress.

## When to stop

- **A hotspot:** the hunter reports it clean after a thorough look (fine — go to the next).
- **The whole hunt:** the user interrupts; the assessor finds no significant hotspots (report a clean assessment); the build is broken before any test was added (report it).
- **Not a reason to stop:** a clean hotspot, test infrastructure trouble on one hotspot (report it as unconfirmed), low-confidence findings (report them as suspected).

## With other tools

- A security review asks "can an attacker exploit this?"; the bug hunt asks "will this fail for a normal user?" Run both before a release.
- A code review looks at a diff; the bug hunt looks for bugs in code that already shipped.
