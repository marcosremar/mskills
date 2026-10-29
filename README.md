# mskills

Agent skills. Invoke one with `/mskills-<name>`.

| Name | Command | Does |
|---|---|---|
| [missing-essentials](missing-essentials/SKILL.md) | `/mskills-missing-essentials` | Walks the code from the entry to the result, then implements only the missing essentials of the project or of one named feature |

Install by linking the skill folder into the agent skills directory under the command name:

```bash
ln -s /path/to/mskills/missing-essentials ~/.agents/skills/mskills-missing-essentials
ln -s ~/.agents/skills/mskills-missing-essentials ~/.claude/skills/mskills-missing-essentials
```

A session that is already open does not load a skill added after it started.
