# Agentic Skills

Reusable skills for AI coding agents and LLM workflows.

This repository contains agent-facing instructions for repeatable workflows such as adversarial review, debugging, planning, and evaluation. The skills are written for OpenCode-compatible skill loaders, but the patterns are intended to be useful across agentic development environments.

## Released Skills

| Skill | Use When | Purpose |
|-------|----------|---------|
| [red-team](#red-team) | Reviewing specs, plans, code, or PRs | Adversarial review with fresh eyes |

## Install

Add agent-skills to the `plugin` array in your `opencode.json`:

```json
{
  "plugin": ["agent-skills@git+https://github.com/sfriedman71/skills.git"]
}
```

Restart OpenCode. The plugin registers all skills automatically — no symlinks needed.

For detailed instructions, migration from older installs, and troubleshooting, see [.opencode/INSTALL.md](.opencode/INSTALL.md).

## Incubator

Experimental skills and development notes live in `incubator/`. They are public to show process, but only `agent-skills/` is supported for installation.

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
| **Compete** | "compete", "battle", "two reviewers", "two agents", "contest" | 2 subagents review independently; scored on depth/uniqueness/actionability |

#### Review dimensions

By default, artifacts are assessed across 10 orthogonal dimensions:

1. **Correctness** — flawed reasoning, incorrect assumptions, internal contradictions
2. **Completeness** — missing requirements, unhandled cases, oversimplified descriptions
3. **Clarity** — ambiguous language, undefined terms, vague requirements
4. **Parsimony** — unnecessary complexity, redundancy, over-engineering, scope creep
5. **Feasibility** — can this be built, run, or operated as described?
6. **Resilience** — failure modes, error handling, edge cases, graceful degradation
7. **Verifiability** — testability, acceptance criteria, observability
8. **Data integrity** — atomic values, normalization, unambiguous encoding
9. **Maintainability** — administrative ease, operational burden, clarity of ownership
10. **Security** — input sanitization, permissions, access control, attack surface

Override with custom dimensions:

```
red-team this for security and performance only @docs/api-design.md
```

#### Output format

Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Location** — the specific text, section, or line being critiqued
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared

This distinction matters: confident recommendations save time; flagged alternatives prevent premature commitment to a single approach.
