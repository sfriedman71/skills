# Red-Team Skill Restructuring Design

**Date**: 2026-05-10
**Approach**: Tiered dimensions + two-phase prompt + XML structure. Eliminate compete mode. Single subagent, cost-neutral.

## Motivation

The current 10-dimension single-pass prompt spreads subagent attention too thin across a flat list. The subagent must hold all dimensions in working memory simultaneously, leading to shallow treatment of some and wasted tokens on irrelevant dimensions. The "No issues found" guard and artifact-type weighting prose attempt to compensate but are band-aids on a structural problem.

The fix: split dimensions into tiers (always-assess vs. select-based-on-artifact), structure the subagent's work into a scan phase (identify what matters) and an analyze phase (deep-dive on what matters), and use XML tags to make the prompt structure explicit. This focuses attention without increasing cost — same single subagent, same number of dispatches.

## 1. Dimension Tiers

### Core Dimensions (always assess)

These apply to every artifact type. No selection needed.

1. **Correctness** — Flawed reasoning, incorrect assumptions, internal contradictions between sections. Does the artifact arrive at valid conclusions from valid premises? Do different parts tell the same story?
2. **Completeness** — Missing requirements, unhandled cases, unspecified behavior, oversimplified descriptions that omit necessary detail. Is everything that should be specified actually specified?
3. **Clarity** — Language open to multiple interpretations, undefined terms, vague requirements, ambiguous references. Could two reasonable readers understand this differently?

### Context Dimensions (select 2-4 based on artifact)

4. **Parsimony** — Unnecessary complexity, redundancy, over-engineering, premature abstraction, scope creep. Does every element earn its place?
5. **Feasibility** — Can this actually be built, run, or operated as described? Are the dependencies realistic? Is the timeline achievable? Are the technical claims grounded?
6. **Resilience** — Failure modes, error handling, edge cases, graceful degradation. What happens when things go wrong? Are the unhappy paths addressed with the same rigor as the happy path?
7. **Verifiability** — Testability, acceptance criteria, observability. Can you confirm this works? Are the success conditions defined precisely enough to check?
8. **Data integrity** — Atomic values, appropriate normalization, no ambiguous encoding, no redundant representation. Are data structures clean and unambiguous?
9. **Maintainability** — Administrative ease, operational burden, clarity of ownership, ease of modification. Will this be manageable after it's built?
10. **Security** — Input sanitization, permissions, access control, attack surface. Are the trust boundaries defined and enforced?

### Selection Guidance

The scan phase selects context dimensions based on the artifact. Typical patterns (not hard rules):

- **Specs**: Feasibility, Verifiability
- **Code**: Security, Data integrity, Resilience
- **Plans**: Parsimony, Verifiability
- **Data models**: Data integrity, Maintainability

The subagent can deviate if the artifact warrants it — e.g., a "spec with significant data modeling" might add Data integrity.

### Custom Overrides

Users may still override with custom dimensions (e.g., "red-team this for security and performance only"). Custom overrides replace the entire dimension set — both core and context tiers.

## 2. Two-Phase Prompt Structure

The subagent prompt is restructured into two phases using XML tags:

```xml
You are reviewing an artifact with fresh eyes.

<core_dimensions>
1. Correctness — Flawed reasoning, incorrect assumptions, internal contradictions between sections. Does the artifact arrive at valid conclusions from valid premises? Do different parts tell the same story?
2. Completeness — Missing requirements, unhandled cases, unspecified behavior, oversimplified descriptions that omit necessary detail. Is everything that should be specified actually specified?
3. Clarity — Language open to multiple interpretations, undefined terms, vague requirements, ambiguous references. Could two reasonable readers understand this differently?
</core_dimensions>

<context_dimensions>
Select 2-4 most relevant to this artifact:
4. Parsimony — Unnecessary complexity, redundancy, over-engineering, premature abstraction, scope creep. Does every element earn its place?
5. Feasibility — Can this actually be built, run, or operated as described? Are the dependencies realistic? Is the timeline achievable? Are the technical claims grounded?
6. Resilience — Failure modes, error handling, edge cases, graceful degradation. What happens when things go wrong? Are the unhappy paths addressed with the same rigor as the happy path?
7. Verifiability — Testability, acceptance criteria, observability. Can you confirm this works? Are the success conditions defined precisely enough to check?
8. Data integrity — Atomic values, appropriate normalization, no ambiguous encoding, no redundant representation. Are data structures clean and unambiguous?
9. Maintainability — Administrative ease, operational burden, clarity of ownership, ease of modification. Will this be manageable after it's built?
10. Security — Input sanitization, permissions, access control, attack surface. Are the trust boundaries defined and enforced?
</context_dimensions>

<phase name="scan">
Read the artifact. For each core dimension, note whether issues exist. For context dimensions, select 2-4 most relevant to this artifact and note whether issues exist.

Output a shortlist in this format:
- DIMENSION NAME: brief signal — what caught your attention, or "no issues"

Only list dimensions where you found issues or selected them for deeper analysis. Do not list dimensions you did not select and found no issues in.
</phase>

<phase name="analyze">
For each dimension on your shortlist that has issues, produce a full issue report.

<output_format>
For each issue:
- **Dimension** — which dimension
- **Severity** — critical, major, or minor
- **Location** — cite the specific text, section, or line
- **Description** — what the issue is
- **Recommendation** — ONLY if you have high confidence in the solution
- **Alternative note** — if low confidence or multiple viable solutions exist, flag for comparison/decision instead of recommending one
</output_format>

Rank all issues from critical to minor.
</phase>

Do not be polite. Do not hedge. Find real problems.
```

### What This Removes

- "No issues found for [dimension]" guard — unnecessary because unselected dimensions are simply not reported
- "Weight your analysis toward..." prose — replaced by structural tier selection in the scan phase
- Flat dimension list — replaced by `<core_dimensions>` and `<context_dimensions>` tags

### What This Keeps

- Recommendation/alternative note distinction
- Location citation requirement
- "Do not be polite. Do not hedge. Find real problems."
- Custom dimension overrides (replace the entire dimension set)

## 3. Eliminate Compete Mode

Compete mode is removed entirely. The skill becomes single-mode.

**Rationale**: The two-phase prompt already gives the subagent more focused, structured attention. The original purpose of compete mode was to compensate for a single reviewer's shallow attention — if the reviewer is doing a focused scan-then-analyze, the competitive pressure is less necessary. The cost of a second agent is no longer justified by the marginal quality improvement.

**Trigger phrases for compete mode** ("compete", "battle", "two reviewers", etc.) are removed. If users used compete mode before, they get the same single-mode review with better focus.

**Scoring rubric and convergence/divergence fusion** are removed along with compete mode.

## 4. Skill File Layout

```markdown
---
name: red-team
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, or security issues. Triggers include red-team, fresh eyes, critique.
---

# Red Team

One-paragraph description of what the skill does.

## Dimensions

<core_dimensions> — 3 always-assess dimensions with descriptions
</core_dimensions>

<context_dimensions> — 7 selectable dimensions with descriptions
</context_dimensions>

Custom override instructions.

## Instructions

1. Read the artifact.
2. Dispatch one subagent with the critique prompt below.
3. Return the severity-ranked issue list.

## Critique Prompt

Full two-phase prompt with XML tags as shown in Section 2.

## Trigger Phrases

- "red-team this [artifact]"
- "fresh eyes on this [artifact]"
- "critique this [artifact]"
```

### Sections Removed from Current Skill

- **Modes** section (no longer needed — single mode)
- **Compete Mode** subsection
- **Scoring rubric**
- **Convergence/divergence fusion**

### Sections Changed

- **Default Dimensions** → **Dimensions** (with tiered XML structure)
- **Subagent Critique Prompt** → **Critique Prompt** (two-phase XML structure)

## 5. README and CHANGELOG Updates

The README must be updated to:
- Remove compete mode documentation
- Show tiered dimensions instead of flat list
- Describe the two-phase prompt structure
- Update the modes table (single mode only)
- Remove scoring rubric and convergence/divergence descriptions

The CHANGELOG gets a new entry under Unreleased documenting the restructuring.

## Impact Summary

| Aspect | Before | After |
|--------|--------|-------|
| Dimensions | 10 flat, all assessed equally | 3 core (always) + 7 context (select 2-4) |
| Prompt | Single-phase, flat list | Two-phase (scan + analyze), XML-structured |
| Compete mode | Two agents, scoring, fusion | Eliminated |
| "No issues found" guard | Required per dimension | Unnecessary (unselected dims not reported) |
| Artifact-type weighting | Prose suggestion | Structural (scan phase selects context dims) |
| Cost | 1 subagent (single) or 2 (compete) | 1 subagent, always |

## Out of Scope

- Hardcoded artifact-type dimension profiles (could be added later as a refinement)
- Re-examination / self-challenge phase (could be added as an optional trigger)
- More than 10 dimensions
