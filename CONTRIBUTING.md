# Contributing to agent-grimoire

Thanks for adding to the grimoire! Here's how to contribute a skill.

## Before You Start

Ask yourself:
- **Is this skill useful?** Would others benefit from it?
- **Is it reusable?** Does it work across different projects/contexts?
- **Is it focused?** Does it do one thing well?

## Creating a skill

### 1. Structure

Create a folder in `skills/` with your skill name:

```
skills/your-skill-name/
├── SKILL.md          # Required
├── REFERENCE.md      # Optional: detailed docs
├── EXAMPLES.md       # Optional: more examples
└── scripts/          # Optional: helper scripts
    └── validate.py
```

### 2. SKILL.md Requirements

Every spell needs a `SKILL.md` with YAML frontmatter:

```yaml
---
name: your-skill-name
description: Clear description of what this skill does and when the agent should use it.
---

# Your Skill Name

## Instructions

Clear, step-by-step guidance. Write this like you're onboarding a new team member.

## Examples

Show concrete usage. Agents learn from examples.
```

**Name rules:**
- Max 64 characters
- Lowercase letters, numbers, and hyphens only
- No spaces, underscores, or special characters
- Cannot include "anthropic" or "claude"

**Description rules:**
- Max 1024 characters
- Be specific about *when* to trigger the spell
- Include keywords the agent might encounter

### 3. Writing Good Instructions

**Do:**
- Be specific and actionable
- Use step-by-step formatting
- Include code examples
- Reference additional files when needed (the agent can read them)

**Don't:**
- Be vague ("write good code")
- Include unnecessary context
- Exceed ~5000 tokens in SKILL.md (use additional files for detailed references)

### 4. Scripts

If your skill includes scripts:
- Make them executable (`chmod +x`)
- Include a shebang (`#!/usr/bin/env python3`)
- Handle errors gracefully
- Output clear, parseable results
- Don't require external dependencies that aren't commonly available

## Submitting

1. **Fork** this repository
2. **Create** your skill in `skills/your-skill-name/`
3. **Test** it locally with Claude Code or the API
4. **Submit** a pull request

### PR Checklist

- [ ] `SKILL.md` has valid YAML frontmatter
- [ ] Name follows conventions (lowercase, hyphens)
- [ ] Description clearly states when to use the skill
- [ ] Instructions are actionable and specific
- [ ] Tested with an actual agent
- [ ] No sensitive data or credentials

## Categories

Organize your skill by what it does. Some categories to consider:

- **Frameworks** — React, Vue, Next.js, Rails, etc.
- **Languages** — TypeScript, Python, Rust, Go, etc.
- **Workflows** — Code review, testing, deployment, etc.
- **Tools** — Git, Docker, databases, etc.
- **Domains** — Security, accessibility, performance, etc.

## Questions?

Open an issue. We're happy to help shape your skill before you submit.
