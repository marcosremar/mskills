# mskills

Agent skills. Invoke one with `/mskills-<name>`.

| Name | Command | Does |
|---|---|---|
| [missing-essentials](missing-essentials/SKILL.md) | `/mskills-missing-essentials` | Walks the code from the entry to the result, then implements only the missing essentials of the project or of one named feature |
| [bug-hunt](bug-hunt/SKILL.md) | `/mskills-bug-hunt` | Finds where bugs are likely (complexity, missing tests, risky structure), then writes a failing test for each suspected bug before reporting it; fixes only on request |

Install by linking the skill folder into the agent skills directory under the command name:

```bash
ln -s /path/to/mskills/missing-essentials ~/.agents/skills/mskills-missing-essentials
ln -s ~/.agents/skills/mskills-missing-essentials ~/.claude/skills/mskills-missing-essentials
```

bug-hunt also ships two agent definitions in `bug-hunt/agents/`. Link them into `~/.claude/agents/` to get the `swe-bug-assessor` and `swe-bug-hunter` agent types; without them the skill runs general-purpose agents that read those files.

```bash
ln -s /path/to/mskills/bug-hunt ~/.agents/skills/mskills-bug-hunt
ln -s ~/.agents/skills/mskills-bug-hunt ~/.claude/skills/mskills-bug-hunt
ln -s /path/to/mskills/bug-hunt/agents/swe-bug-assessor.md ~/.claude/agents/swe-bug-assessor.md
ln -s /path/to/mskills/bug-hunt/agents/swe-bug-hunter.md ~/.claude/agents/swe-bug-hunter.md
```

A session that is already open does not load a skill added after it started.
