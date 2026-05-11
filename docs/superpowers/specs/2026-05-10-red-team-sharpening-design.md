# Red-Team Skill Sharpening Design

**Date**: 2026-05-10
**Approach**: Targeted fixes — low-risk, high-impact improvements across dimensions, critique prompt, and compete mode.

## 1. Dimensions

### Problems with the Original Dimensions

The original 11 dimensions had significant overlap and gaps:

- **Oversimplifications ≈ Omissions**: Oversimplifying is omitting necessary detail while implying completeness. Reviewers can't cleanly distinguish them.
- **Design best practices is a grab bag**: Every issue could be reframed as a "design best practice" violation. It catches everything and nothing.
- **Redundancies ⊂ Unnecessary complexities**: Already identified; redundancy is a subset.
- **1NF is domain-specific**: Database normalization doesn't apply to most artifact types.
- **Security is a domain lens, not a dimension**: Security issues are really instances of other problems (logical error in auth, omission of input validation). But it's too important to drop — kept as an explicit lens.
- **Missing: Consistency**: Internal contradictions between sections. Not a logical error (both parts may be individually valid), not an omission (both exist). A distinct problem.
- **Missing: Resilience**: Failure modes, error handling, edge cases. Happy-path logic might be sound, but unhappy paths unaddressed.
- **Missing: Verifiability**: Can the claims be checked? Are acceptance criteria defined? Something can be clear but untestable.

### New Dimension List (10 dimensions)

Each dimension is mutually exclusive, jointly exhaustive, and actionable — the name tells the reviewer what to look for.

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

### Mapping from Old to New

| Old dimension | New home |
|---------------|----------|
| Logical errors | Correctness |
| Oversimplifications | Completeness |
| Omissions | Completeness |
| Redundancies | Parsimony |
| Unnecessary complexities | Parsimony |
| Design best practices | Distributed across dimensions (was too vague to be useful) |
| Implementation feasibility | Feasibility |
| Scope creep detection | Parsimony |
| 1NF | Data integrity |
| Maintainability | Maintainability |
| Security | Security |
| *(new)* | Clarity, Resilience, Verifiability |

## 2. Critique Prompt

### Current Problems

- Treats all artifact types identically
- Allows vague findings without citations
- Silently skips dimensions with no issues (ambiguous — was it considered or forgotten?)

### Changes

**Artifact-type awareness**: Add one sentence to the critique prompt instructing the subagent to weight dimensions by artifact relevance:

> Weight your analysis toward dimensions most relevant to the artifact type (e.g., clarity and feasibility for specs; security and data integrity for code; parsimony and verifiability for plans).

**Require specificity**: Add a citation requirement to the output format:

> - **Location** — cite the specific text, section, or line you are critiquing

**No-issues-found guard**: Add explicit instruction:

> For each dimension where you find no issues, state "No issues found for [dimension]" rather than omitting it.

### Updated Subagent Critique Prompt

```
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
| Dimensions | Overlapping (oversimplifications≈omissions, redundancies⊂complexities), vague (design best practices), domain-specific (1NF), missing coverage (consistency, resilience, verifiability) | 10 orthogonal dimensions with detailed descriptions: correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, security |
| Critique prompt | Generic across artifact types; allows vague findings; silently skips dimensions | Artifact-type weighting; requires citations; explicit no-issues-found guard |
| Compete mode | Arbitrary 5-point award; vague "deduplicate" | Scoring rubric (depth/uniqueness/actionability); structured convergence/divergence fusion |

## Out of Scope

- Artifact-type-dependent dimension sets (structural overhaul — could follow as a second pass)
- Adversarial framing techniques in the prompt (depth-first prompt improvement — could follow as a second pass)
- New modes beyond single/compete
