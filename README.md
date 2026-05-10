# Agentic Skills

Reusable skills for AI coding agents and LLM workflows.

This repository contains agent-facing instructions for repeatable workflows such as adversarial review, debugging, planning, and evaluation. The skills are written for OpenCode-compatible skill loaders, but the patterns are intended to be useful across agentic development environments.

## Released Skills

| Skill | Use When | Purpose |
|-------|----------|---------|
| [red-team](#red-team) | Reviewing specs, plans, code, or PRs | Adversarial review with fresh eyes |

## Install

Released skills live in `skills/`.

To install all released skills for OpenCode:

```bash
mkdir -p ~/.config/opencode/skills
ln -s ~/skills/skills/* ~/.config/opencode/skills/
```

To install one skill:

```bash
ln -s ~/skills/skills/red-team ~/.config/opencode/skills/red-team
```

Replace `~/skills` with your actual checkout path if different.

## Incubator

Experimental skills and development notes live in `incubator/`. They are public to show process, but only `skills/` is supported for installation.

## Skill Lifecycle

See `docs/skill-lifecycle.md` for authoring standards, evaluation expectations, and the promotion checklist.

## License

MIT. You may use, adapt, and redistribute these skills.

---

## red-team

### What it does

The **red-team** skill spawns one or two subagents to critically review any artifact with fresh eyes — specs, plans, designs, code, PRs, or documents. It returns a severity-ranked issue list across multiple review dimensions, with recommendations where there's high confidence and alternative notes where there isn't.

### Why it's useful

When an AI agent reviews its own work, it faces conflicting goals: it wants to be helpful and positive, but also critical and honest. LLMs do poorly with conflicting goals. They tend to go easy on their own output.

Adversarial review solves this by having a **different** agent do the review — one that didn't produce the work and has no investment in it. This eliminates the conflict of interest and produces dramatically better critiques.

The **compete mode** takes this further: two subagents review the same artifact independently, competing to find the most valuable issues. Competitive pressure incentivizes deeper analysis and surfaces problems that a single reviewer might miss.

Inspired by [Jesse Vincent's post on adversarial review](https://blog.fsck.com/2026/05/01/adversarial-review/), which showed that even a simple "look at this again with fresh eyes" produces startlingly good results — and that telling reviewers they're being evaluated against a competitor makes them work harder.

### When to use it

- Before committing a spec or design doc
- After drafting an implementation plan
- Before merging a significant PR
- Any time you want an honest second opinion on something you (or an agent) just produced

### How to use it

In opencode, reference a file and use a trigger phrase:

```
red-team this @docs/specs/my-feature.md
```

```
fresh eyes on this @plans/sprint-3.md
```

```
critique this plan, compete mode @docs/design.md
```

#### Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Single** (default) | "red-team", "fresh eyes", "critique" | 1 subagent reviews |
| **Compete** | "compete", "battle", "two reviewers", "two agents", "contest" | 2 subagents review independently; winner gets 5 points |

#### Review dimensions

By default, artifacts are assessed across 11 dimensions:

1. **Logical errors** — flawed reasoning, incorrect assumptions
2. **Redundancies** — duplicated logic, overlapping concerns
3. **Oversimplifications** — hand-waved complexity, missing edge cases
4. **Omissions** — forgotten requirements, unspecified behavior
5. **Unnecessary complexities** — over-engineering, premature abstraction
6. **Design best practices** — violations of established patterns
7. **Implementation feasibility** — can this actually be built as described?
8. **Scope creep detection** — features that don't belong in the current scope
9. **First normal form (1NF)** — atomic values, no repeating groups, no duplicate rows
10. **Maintainability** — administrative ease, operational burden, clarity of ownership
11. **Security** — input sanitization, permissions, RBAC/ABAC/AcBAC/RuBAC access control

Override with custom dimensions:

```
red-team this for security and performance only @docs/api-design.md
```

#### Output format

Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared

This distinction matters: confident recommendations save time; flagged alternatives prevent premature commitment to a single approach.
