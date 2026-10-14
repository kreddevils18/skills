# skills

Agent skills by [Kien Hoang](https://kien-hoang.vercel.app/skills). Each skill is a folder with a `SKILL.md`, the open skill format, so it works in Claude Code, Codex, Cursor, OpenCode and any other agent that reads skills.

Browse them with install commands and the full instructions at [kien-hoang.vercel.app/skills](https://kien-hoang.vercel.app/skills).

## Skills

| Skill | What it does |
| --- | --- |
| [smart-app-banner](skills/smart-app-banner) | Adds, fixes or checks Apple's Smart App Banner (`apple-itunes-app` meta tag) so a website promotes its iOS app in Safari. |

## Install

With the [skills CLI](https://github.com/vercel-labs/skills), install one skill:

```sh
npx skills add https://github.com/kreddevils18/skills --skill smart-app-banner
```

Or every skill in this repository:

```sh
npx skills add https://github.com/kreddevils18/skills
```

The CLI asks which agents to install for. To skip the questions, name the agents and install for every project (`-g`):

```sh
npx skills add https://github.com/kreddevils18/skills --skill smart-app-banner -g -a claude-code -a codex
```

List the skills without installing anything:

```sh
npx skills add https://github.com/kreddevils18/skills --list
```

### Copy it yourself

```sh
git clone https://github.com/kreddevils18/skills
cp -R skills/skills/smart-app-banner ~/.claude/skills/
```

| Agent | Every project | One repository |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/<skill>` | `.claude/skills/<skill>` |
| Codex | `~/.agents/skills/<skill>` | `.agents/skills/<skill>` |
| Other agents | the agent's skills folder | the agent's project skills folder |

Keep one copy per agent, or the agent sees the skill twice. Restart the agent after installing.

## Security audits

Every skill is audited before it is published, with the `audit` mode of the `ak:skill-creator` skill: structure and frontmatter validation, prompt lint, and a manual review for executable code, secrets, network access and data exfiltration, instruction overrides and dependencies.

Results live in [`audits/`](audits), one JSON file per skill. Each records the SHA-256 of the `SKILL.md` it covers, so a changed skill shows as not yet audited until it is audited again.

| Skill | Result | Audited |
| --- | --- | --- |
| smart-app-banner | Pass, 7 of 7 checks | 2026-10-14 |

## Layout

```
skills/
  <skill-name>/
    SKILL.md     instructions the agent loads
    README.md    what the skill does and how to install it
audits/
  <skill-name>.json   latest security audit
```

## License

MIT
