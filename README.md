# Skills

Personal agent skills for opencode and compatible AI coding agents.

## Structure

Each skill lives in `skills/<name>/SKILL.md` following the [opencode skill spec](https://opencode.ai/docs/skills/).

## Install

Symlink into your opencode config:

```bash
ln -s ~/skills/skills/* ~/.config/opencode/skills/
```

Or symlink individual skills:

```bash
ln -s ~/skills/skills/red-team ~/.config/opencode/skills/red-team
```

## Skills

| Skill | Description |
|-------|-------------|
| red-team | Critically review specs, plans, code, or any artifact for errors, omissions, and design flaws |
