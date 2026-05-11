# Red-Team Skill Sharpening Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Sharpen the red-team skill with orthogonal dimensions, a stronger critique prompt, and structured compete mode.

**Architecture:** Three targeted changes to `agent-skills/red-team/SKILL.md`, with corresponding updates to `README.md` and `CHANGELOG.md`. No new files, no scripts, no tests (repo has no test framework).

**Tech Stack:** Markdown skill files only.

**Editing convention:** All edits use content-based matching (oldString → newString), not line numbers. Line numbers shift after each edit, so content matching is the only reliable approach. If any edit fails because the target content doesn't match, read the file, find the correct section by heading name, and adapt the edit.

---

### Task 1: Update dimensions in SKILL.md

**Files:**
- Modify: `agent-skills/red-team/SKILL.md`

- [ ] **Step 1: Update the frontmatter description**

Match and replace the `description` field in the YAML frontmatter:

**oldString:**
```
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for logical errors, omissions, oversimplifications, unnecessary complexities, design flaws, feasibility issues, or scope creep. Triggers include red-team, fresh eyes, critique.
```

**newString:**
```
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, or security issues. Triggers include red-team, fresh eyes, critique.
```

- [ ] **Step 2: Replace the Default Dimensions section**

Match and replace the entire `## Default Dimensions` section through the custom dimensions line:

**oldString:**
```
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
```

**newString:**
```
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
```

- [ ] **Step 3: Verify — search for old dimension names**

Run: `rg "Logical errors|Redundancies|Oversimplifications|Omissions|Unnecessary complexities|Design best practices|Implementation feasibility|Scope creep detection|1NF|First normal form" agent-skills/red-team/SKILL.md`

Expected: no matches. If matches remain, the old content was not fully replaced.

Also verify the new dimensions are present:

Run: `rg "Correctness|Completeness|Clarity|Parsimony|Feasibility|Resilience|Verifiability|Data integrity|Maintainability|Security" agent-skills/red-team/SKILL.md`

Expected: all 10 dimension names appear.

- [ ] **Step 4: Commit**

```bash
git add agent-skills/red-team/SKILL.md
git commit -m "Replace overlapping dimensions with orthogonal set

10 dimensions: correctness, completeness, clarity, parsimony,
feasibility, resilience, verifiability, data integrity,
maintainability, security. Each is mutually exclusive and
actionable with a detailed description."
```

---

### Task 2: Update critique prompt in SKILL.md

**Files:**
- Modify: `agent-skills/red-team/SKILL.md`

- [ ] **Step 1: Replace the Subagent Critique Prompt section**

Match and replace the entire `## Subagent Critique Prompt` section:

**oldString:**
```
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
```

**newString:**
```
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
```

- [ ] **Step 2: Verify — search for new prompt features**

Run: `rg "Weight your analysis|No issues found|Location" agent-skills/red-team/SKILL.md`

Expected: all three patterns match. If any is missing, the replacement was incomplete.

- [ ] **Step 3: Commit**

```bash
git add agent-skills/red-team/SKILL.md
git commit -m "Sharpen critique prompt: artifact-type weighting, citations, no-issues-found guard"
```

---

### Task 3: Update compete mode in SKILL.md

**Files:**
- Modify: `agent-skills/red-team/SKILL.md`

- [ ] **Step 1: Replace the Compete Mode section**

Match and replace the `### Compete Mode` instructions:

**oldString:**
```
### Compete Mode

1. Read the user-referenced artifact.
2. Dispatch two subagents independently. Give the first agent dimensions in default order; give the second agent the same dimensions in reverse order. This ensures different attention patterns.
3. Consolidate findings: deduplicate, note which reviewer found each issue.
4. Award 5 points to the reviewer contributing the most valuable feedback. Briefly justify the winner.
5. Include any unique catches from the loser that the winner missed.
```

**newString:**
```
### Compete Mode

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

- [ ] **Step 2: Verify — search for old compete mode language**

Run: `rg "Award 5 points|deduplicate|the loser" agent-skills/red-team/SKILL.md`

Expected: no matches. If matches remain, the old content was not fully replaced.

Run: `rg "Depth \(0-2\)|Convergent|Divergent|lower-scoring" agent-skills/red-team/SKILL.md`

Expected: all patterns match.

- [ ] **Step 3: Commit**

```bash
git add agent-skills/red-team/SKILL.md
git commit -m "Replace arbitrary compete scoring with depth/uniqueness/actionability rubric and structured convergence/divergence fusion"
```

---

### Task 4: Update README.md

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Update the compete mode table row**

Match and replace:

**oldString:**
```
| **Compete** | "compete", "battle", "two reviewers", "two agents", "contest" | 2 subagents review independently; winner gets 5 points |
```

**newString:**
```
| **Compete** | "compete", "battle", "two reviewers", "two agents", "contest" | 2 subagents review independently; scored on depth/uniqueness/actionability |
```

- [ ] **Step 2: Update the Review dimensions section**

Match and replace:

**oldString:**
```
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
```

**newString:**
```
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
```

- [ ] **Step 3: Update the Output format section**

Match and replace:

**oldString:**
```
Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared
```

**newString:**
```
Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Location** — the specific text, section, or line being critiqued
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared
```

- [ ] **Step 4: Verify — search for old content**

Run: `rg "5 points|11 dimensions|Logical errors|Redundancies|Oversimplifications|1NF|First normal form" README.md`

Expected: no matches.

Run: `rg "10 orthogonal|Correctness|Completeness|Clarity|Parsimony|Feasibility|Resilience|Verifiability|Data integrity|Location" README.md`

Expected: all patterns match.

- [ ] **Step 5: Commit**

```bash
git add README.md
git commit -m "Update README to reflect new dimensions, scoring, and output format"
```

---

### Task 5: Update CHANGELOG.md

**Files:**
- Modify: `CHANGELOG.md`

- [ ] **Step 1: Add changelog entry under Unreleased**

Match and replace the existing Unreleased section:

**oldString:**
```
## Unreleased

- Added repository management structure: released skills in `agent-skills/`, incubator for experiments, lifecycle documentation, MIT license.
- Documented existing `red-team` skill.
```

**newString:**
```
## Unreleased

- Redesigned review dimensions: 10 orthogonal dimensions (correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, security) replacing the original 11 overlapping ones.
- Sharpened critique prompt: artifact-type weighting, citation requirement, no-issues-found guard.
- Replaced compete mode scoring: depth/uniqueness/actionability rubric (0-6) instead of arbitrary 5 points; structured convergence/divergence fusion instead of vague deduplication.
- Added repository management structure: released skills in `agent-skills/`, incubator for experiments, lifecycle documentation, MIT license.
- Documented existing `red-team` skill.
```

- [ ] **Step 2: Verify — search for new entries**

Run: `rg "orthogonal dimensions|artifact-type weighting|convergence/divergence" CHANGELOG.md`

Expected: all three patterns match.

- [ ] **Step 3: Commit**

```bash
git add CHANGELOG.md
git commit -m "Add changelog entries for red-team skill sharpening"
```
