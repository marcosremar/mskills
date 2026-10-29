# mskills

Agent skills. Invoke one with `/mskills-<name>`.

| Name | Command | Stops |
|---|---|---|
| [essentials](essentials/SKILL.md) | `/mskills-essentials` | "Implement everything that's missing" before the essential path is proven |

Install by linking the skill folder into the agent skills directory under the command name:

```bash
ln -s /path/to/mskills/essentials ~/.agents/skills/mskills-essentials
ln -s ~/.agents/skills/mskills-essentials ~/.claude/skills/mskills-essentials
```

A session that is already open does not load a skill added after it started.
