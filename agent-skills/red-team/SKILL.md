---
name: red-team
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, or security issues. Triggers include red-team, fresh eyes, critique.
---

# Red Team

Spawn subagent(s) with fresh eyes to critically assess any artifact — specs, plans, designs, code, PRs, or documents.

## Modes

**Single** (default) — 1 subagent reviews the artifact.

**Compete** — 2 subagents review independently. Triggered by: "compete", "battle", "two reviewers", "two agents", "contest".

## Default Dimensions

1. **Correctness** — Flawed reasoning, incorrect assumptions, internal contradictions between sections. Does the artifact arrive at valid conclusions from valid premises? Do different parts tell the same story?
2. **Completeness** — Missing requirements, unhandled cases, unspecified behavior, oversimplified descriptions that omit necessary detail. Is everything that should be specified actually specified?
3. **Clarity** — Language open to multiple interpretations, undefined terms, vague requirements, ambiguous references. Could two reasonable readers understand this differently?
4. **Parsimony** — Unnecessary complexity, redundancy, over-engineering, premature abstraction, scope creep. Does every element earn its place?
5. **Feasibility** — Can this actually be built, run, or operated as described? Are the dependencies realistic? Is the timeline achievable? Are the technical claims grounded?
6. **Resilience** — Failure modes, error handling, edge cases, graceful degradation. What happens when things go wrong? Are the unhappy paths addressed with the same rigor as the happy path?
7. **Verifiability** — Testability, acceptance criteria, observability. Can you confirm this works? Are the success conditions defined precisely enough to check?
8. **Data integrity** — Atomic values, appropriate normalization, no ambiguous encoding, no redundant representation. Are data structures clean and unambiguous?
9. **Maintainability** — Administrative ease, operational burden, clarity of ownership, ease of modification. Will this be manageable after it's built?
10. **Security** — Input sanitization, permissions, access control, attack surface. Are the trust boundaries defined and enforced?

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

Weight your analysis toward dimensions most relevant to the artifact type (e.g., clarity and feasibility for specs; security and data integrity for code; parsimony and verifiability for plans).

For each dimension where you find no issues, state "No issues found for [dimension]" rather than omitting it.

For each issue found, return:

- **Dimension tag** — which dimension this issue falls under
- **Severity** — critical, major, or minor
- **Location** — cite the specific text, section, or line you are critiquing
- **Description** — what the issue is
- **Recommendation** — ONLY if you have high confidence in the solution
- **Alternative note** — if low confidence or multiple viable solutions exist, flag for comparison/decision instead of recommending one

Rank all issues from critical to minor.

Do not be polite. Do not hedge. Find real problems.

## Trigger Phrases

- "red-team this [artifact]"
- "fresh eyes on this [artifact]"
- "critique this [artifact]"
