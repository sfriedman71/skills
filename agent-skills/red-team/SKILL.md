---
name: red-team
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for logical errors, omissions, oversimplifications, unnecessary complexities, design flaws, feasibility issues, or scope creep. Triggers include red-team, fresh eyes, critique.
---

# Red Team

Spawn subagent(s) with fresh eyes to critically assess any artifact — specs, plans, designs, code, PRs, or documents.

## Modes

**Single** (default) — 1 subagent reviews the artifact.

**Compete** — 2 subagents review independently. Triggered by: "compete", "battle", "two reviewers", "two agents", "contest".

## Default Dimensions

1. Logical errors
2. Redundancies
3. Oversimplifications
4. Omissions
5. Unnecessary complexities
6. Design best practices
7. Implementation feasibility
8. Scope creep detection
9. First normal form (1NF) — atomic values, no repeating groups, no duplicate rows
10. Maintainability — administrative ease, operational burden, clarity of ownership
11. Security — input sanitization, permissions, RBAC/ABAC/AcBAC/RuBAC access control

Users may override with custom dimensions (e.g., "red-team this for security and performance only").

## Instructions

### Single Mode

1. Read the user-referenced artifact.
2. Dispatch one subagent with the critique prompt below.
3. Return the subagent's severity-ranked issue list.

### Compete Mode

1. Read the user-referenced artifact.
2. Dispatch two subagents independently. Give the first agent dimensions in default order; give the second agent the same dimensions in reverse order. This ensures different attention patterns.
3. Consolidate findings: deduplicate, note which reviewer found each issue.
4. Award 5 points to the reviewer contributing the most valuable feedback. Briefly justify the winner.
5. Include any unique catches from the loser that the winner missed.

## Subagent Critique Prompt

You are reviewing an artifact with fresh eyes. Assess it across the following dimensions:

{dimensions}

For each issue found, return:

- **Dimension tag** — which dimension this issue falls under
- **Severity** — critical, major, or minor
- **Description** — what the issue is
- **Recommendation** — ONLY if you have high confidence in the solution
- **Alternative note** — if low confidence or multiple viable solutions exist, flag for comparison/decision instead of recommending one

Rank all issues from critical to minor.

Do not be polite. Do not hedge. Find real problems.

## Trigger Phrases

- "red-team this [artifact]"
- "fresh eyes on this [artifact]"
- "critique this [artifact]"
