# Red-Team Skill Sharpening Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Sharpen the red-team skill with orthogonal dimensions, a stronger critique prompt, and structured compete mode.

**Architecture:** Three targeted changes to `agent-skills/red-team/SKILL.md`, with corresponding updates to `README.md` and `CHANGELOG.md`. No new files, no scripts, no tests (repo has no test framework).

**Tech Stack:** Markdown skill files only.

---

### Task 1: Update dimensions in SKILL.md

**Files:**
- Modify: `agent-skills/red-team/SKILL.md:16-30`

- [ ] **Step 1: Replace the Default Dimensions section**

Replace lines 16-30 (the entire `## Default Dimensions` section through the custom dimensions line) with:

```markdown
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

- [ ] **Step 2: Update the frontmatter description**

Replace line 3 (the `description` field) with:

```yaml
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, or security issues. Triggers include red-team, fresh eyes, critique.
```

- [ ] **Step 3: Read the file and verify dimensions match the spec**

Read `agent-skills/red-team/SKILL.md` and confirm:
- Frontmatter description references the 10 new dimension names
- Default Dimensions section lists all 10 with their descriptions
- No references to old dimension names (logical errors, redundancies, oversimplifications, omissions, unnecessary complexities, design best practices, implementation feasibility, scope creep detection, 1NF)

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
- Modify: `agent-skills/red-team/SKILL.md:48-64`

- [ ] **Step 1: Replace the Subagent Critique Prompt section**

Replace the entire `## Subagent Critique Prompt` section (lines 48-64) with:

```markdown
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

- [ ] **Step 2: Read the file and verify the critique prompt matches the spec**

Read `agent-skills/red-team/SKILL.md` and confirm:
- Artifact-type weighting sentence is present
- "No issues found" guard is present
- "Location" field is in the output format
- Recommendation/alternative note distinction is preserved

- [ ] **Step 3: Commit**

```bash
git add agent-skills/red-team/SKILL.md
git commit -m "Sharpen critique prompt: artifact-type weighting, citations, no-issues-found guard"
```

---

### Task 3: Update compete mode in SKILL.md

**Files:**
- Modify: `agent-skills/red-team/SKILL.md:40-46`

- [ ] **Step 1: Replace the Compete Mode section**

Replace the `### Compete Mode` section (lines 40-46) with:

```markdown
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

- [ ] **Step 2: Read the file and verify compete mode matches the spec**

Read `agent-skills/red-team/SKILL.md` and confirm:
- No "5 points" or "Award" language remains
- Scoring rubric (depth/uniqueness/actionability) with 0-2 scale is present
- Structured fusion (convergent/divergent) replaces "deduplicate"
- "lower-scoring reviewer" replaces "loser"

- [ ] **Step 3: Commit**

```bash
git add agent-skills/red-team/SKILL.md
git commit -m "Replace arbitrary compete scoring with depth/uniqueness/actionability rubric and structured convergence/divergence fusion"
```

---

### Task 4: Update README.md

**Files:**
- Modify: `README.md:85-101`

- [ ] **Step 1: Update the compete mode table row**

Replace line 85:

```markdown
| **Compete** | "compete", "battle", "two reviewers", "two agents", "contest" | 2 subagents review independently; winner gets 5 points |
```

with:

```markdown
| **Compete** | "compete", "battle", "two reviewers", "two agents", "contest" | 2 subagents review independently; scored on depth/uniqueness/actionability |
```

- [ ] **Step 2: Update the Review dimensions section**

Replace lines 89-101:

```markdown
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

with:

```markdown
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

Replace lines 111-117:

```markdown
Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared
```

with:

```markdown
Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Location** — the specific text, section, or line being critiqued
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared
```

- [ ] **Step 4: Read the file and verify all changes**

Read `README.md` and confirm:
- Compete mode row no longer mentions "5 points"
- Dimension count is 10, not 11
- All 10 new dimensions are listed with descriptions
- Old dimension names are gone
- Output format includes Location field

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

After the existing Unreleased entries (line 9), add:

```markdown
- Redesigned review dimensions: 10 orthogonal dimensions (correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, security) replacing the original 11 overlapping ones.
- Sharpened critique prompt: artifact-type weighting, citation requirement, no-issues-found guard.
- Replaced compete mode scoring: depth/uniqueness/actionability rubric (0-6) instead of arbitrary 5 points; structured convergence/divergence fusion instead of vague deduplication.
```

- [ ] **Step 2: Read the file and verify the entry**

Read `CHANGELOG.md` and confirm the new entries are under `## Unreleased` and before any dated headings.

- [ ] **Step 3: Commit**

```bash
git add CHANGELOG.md
git commit -m "Add changelog entries for red-team skill sharpening"
```
