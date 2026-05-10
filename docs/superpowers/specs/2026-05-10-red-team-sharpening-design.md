# Red-Team Skill Sharpening Design

**Date**: 2026-05-10
**Approach**: Targeted fixes — low-risk, high-impact improvements across dimensions, critique prompt, and compete mode.

## 1. Dimensions

### Changes

| Action | Dimension | Rationale |
|--------|-----------|-----------|
| Merge | Redundancies → Unnecessary complexities | Redundancy is a subset of unnecessary complexity; having both splits attention on the same problem |
| Generalize | 1NF → Data structure soundness | Database normalization is too domain-specific; the generalized form covers JSON schemas, API contracts, config files, and object models |
| Add | Clarity and ambiguity | Language open to multiple interpretations is a common and dangerous problem in specs/plans that no current dimension catches |

### New Dimension List

1. Logical errors
2. Oversimplifications
3. Omissions
4. Unnecessary complexities (includes redundancies)
5. Design best practices
6. Implementation feasibility
7. Scope creep detection
8. Clarity and ambiguity — language open to multiple interpretations, undefined terms, vague requirements
9. Data structure soundness — atomic values, no ambiguous encoding, no redundant representation, appropriate normalization
10. Maintainability — administrative ease, operational burden, clarity of ownership
11. Security — input sanitization, permissions, access control

Same count (11), but better scoped. Each dimension now covers a distinct concern with minimal overlap.

## 2. Critique Prompt

### Current Problems

- Treats all artifact types identically
- Allows vague findings without citations
- Silently skips dimensions with no issues (ambiguous — was it considered or forgotten?)

### Changes

**Artifact-type awareness**: Add one sentence to the critique prompt instructing the subagent to weight dimensions by artifact relevance:

> Weight your analysis toward dimensions most relevant to the artifact type (e.g., clarity and feasibility for specs; security and data structure for code; scope and sequencing for plans).

**Require specificity**: Add a citation requirement to the output format:

> - **Location** — cite the specific text, section, or line you are critiquing

**No-issues-found guard**: Add explicit instruction:

> For each dimension where you find no issues, state "No issues found for [dimension]" rather than omitting it.

### Updated Subagent Critique Prompt

```
You are reviewing an artifact with fresh eyes. Assess it across the following dimensions:

{dimensions}

Weight your analysis toward dimensions most relevant to the artifact type (e.g., clarity and feasibility for specs; security and data structure for code; scope and sequencing for plans).

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
```

## 3. Compete Mode

### Current Problems

- "Award 5 points" is arbitrary and uninformative
- "Deduplicate" has no structured guidance — how should overlapping vs. unique findings be handled?

### Changes

**Scoring rubric**: Replace "5 points" with a three-criteria rubric, each scored 0-2:

| Criterion | 0 | 1 | 2 |
|-----------|---|---|---|
| **Depth** | Surface-level observations only | Some root-cause analysis | Consistently identifies root causes |
| **Uniqueness** | Only found what the other reviewer found | Some unique catches | Substantially unique findings |
| **Actionability** | Vague or no recommendations | Recommendations present but generic | Specific, implementable recommendations |

Total: 0-6 per reviewer. Highest total wins. Brief justification required.

**Structured fusion**: Replace "deduplicate" with explicit convergence/divergence analysis:

- **Convergent findings** (both reviewers found): Mark as **high confidence**. These are likely real problems.
- **Divergent findings** (only one reviewer found): Mark as **needs verification**. These may be real issues or false positives — the consumer should decide.
- Present convergent findings first, then divergent.

### Updated Compete Mode Instructions

```
1. Read the user-referenced artifact.
2. Dispatch two subagents independently. Give the first agent dimensions in default order; give the second agent the same dimensions in reverse order. This ensures different attention patterns.
3. Score each reviewer:
   - Depth (0-2): surface observations / some root-cause / consistent root-cause analysis
   - Uniqueness (0-2): no unique findings / some unique / substantially unique
   - Actionability (0-2): vague or none / present but generic / specific and implementable
   Total: 0-6. Highest total wins. Briefly justify.
4. Fuse findings:
   - Convergent (both found): mark "high confidence" — likely real problems
   - Divergent (only one found): mark "needs verification" — consumer decides
   - Present convergent findings first, then divergent
5. Include any unique catches from the lower-scoring reviewer that the winner missed.
```

## Impact Summary

| Area | Before | After |
|------|--------|-------|
| Dimensions | 1NF is domain-specific; redundancies overlaps with unnecessary complexities; no ambiguity detection | Generalized data structure dimension; merged overlap; new clarity/ambiguity dimension |
| Critique prompt | Generic across artifact types; allows vague findings; silently skips dimensions | Artifact-type weighting; requires citations; explicit no-issues-found guard |
| Compete mode | Arbitrary 5-point award; vague "deduplicate" | Scoring rubric (depth/uniqueness/actionability); structured convergence/divergence fusion |

## Out of Scope

- Artifact-type-dependent dimension sets (structural overhaul — could follow as a second pass)
- Adversarial framing techniques in the prompt (depth-first prompt improvement — could follow as a second pass)
- New modes beyond single/compete
