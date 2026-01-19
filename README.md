# agent-grimoire

A collection of spells for AI agents.

> *"A spell that can find any four-leaf clover. I think I'll take this one."*
> — Frieren

---

## What is this?

**agent-grimoire** is a community collection of [Agent Skills](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills) — modular capabilities that extend what AI agents can do. Each skill packages instructions, scripts, and resources that agents load on-demand.

Think of it like a spellbook. Each skill teaches your agent something new: React best practices, deployment workflows, code review guidelines, API design patterns, and more.

## Quick Start

### Claude Code

Drop a skill folder into your Claude Code skills directory:

```bash
# Clone the grimoire
git clone https://github.com/anthropics/agent-grimoire.git

# Copy a skill to your local skills folder
cp -r agent-grimoire/skills/react-development ~/.claude/skills/

# Or symlink for easy updates
ln -s $(pwd)/agent-grimoire/skills/react-development ~/.claude/skills/react-development
```

### Claude.ai

Zip a skill folder and upload via Settings > Features.

## Available skills

<!-- SKILL_TABLE_START -->
| Skill                                           | Description                                                            |
| ----------------------------------------------- | ---------------------------------------------------------------------- |
| [code-review](skills/code-review)               | Perform thorough code reviews on pull requests, diffs, or code changes |
| [react-development](skills/react-development) | React patterns, anti-patterns, and performance optimization            |
| [solid-development](skills/solid-development) | SolidJS patterns, reactivity model, and best practices                 |
<!-- SKILL_TABLE_END -->

## Skill Structure

Each skill is a directory with a `SKILL.md` file:

```
skill-name/
├── SKILL.md          # Required: metadata + instructions
├── REFERENCE.md      # Optional: detailed documentation
└── scripts/          # Optional: executable helpers
    └── helper.py
```

### SKILL.md Format

```yaml
---
name: skill-name
description: What this skill does and when to use it.
---

# Skill Name

## Instructions

Step-by-step guidance for the agent.

## Examples

Concrete usage examples.
```

## Contributing

We welcome new spells! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

**Quick version:**
1. Fork this repo
2. Create your spell in `skills/your-spell-name/`
3. Include a `SKILL.md` with proper frontmatter
4. Submit a PR

## License

MIT
