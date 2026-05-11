# Red-Team Skill Restructuring Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restructure the red-team skill with tiered dimensions, a two-phase XML-structured prompt, and compete mode eliminated.

**Architecture:** Replace the current flat SKILL.md with tiered core/context dimensions, a scan+analyze two-phase prompt using XML tags, and single-mode-only instructions. Update README and CHANGELOG to match.

**Tech Stack:** Markdown skill files only.

**Editing convention:** All edits use content-based matching (oldString → newString), not line numbers. If any edit fails because the target content doesn't match, read the file, find the correct section by heading name, and adapt the edit.

---

### Task 1: Rewrite SKILL.md

The skill file changes are extensive enough that a full replacement is cleaner than multiple targeted edits. Replace the entire file content.

**Files:**
- Modify: `agent-skills/red-team/SKILL.md`

- [ ] **Step 1: Replace the entire SKILL.md content**

Match and replace the entire file content from the frontmatter through the last trigger phrase:

**oldString:**
```
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
```

**newString:**
```
---
name: red-team
description: Use when reviewing specs, plans, designs, code, PRs, or any artifact for correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, or security issues. Triggers include red-team, fresh eyes, critique.
---

# Red Team

Spawn a subagent with fresh eyes to critically assess any artifact — specs, plans, designs, code, PRs, or documents. The subagent scans for relevant dimensions, then deep-dives on what matters.

## Dimensions

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

Users may override with custom dimensions (e.g., "red-team this for security and performance only"). Custom overrides replace the entire dimension set — both core and context tiers.

## Instructions

1. Read the user-referenced artifact.
2. Dispatch one subagent with the critique prompt below.
3. Return the subagent's severity-ranked issue list.

## Critique Prompt

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

## Trigger Phrases

- "red-team this [artifact]"
- "fresh eyes on this [artifact]"
- "critique this [artifact]"
```

- [ ] **Step 2: Verify — search for removed content**

Run: `grep -E "Modes|Compete|Single Mode|Compete Mode|No issues found|Weight your analysis|Default Dimensions|Subagent Critique Prompt|depth/uniqueness|convergent|divergent" agent-skills/red-team/SKILL.md`

Expected: no matches (these sections/phrases are all removed).

- [ ] **Step 3: Verify — search for new content**

Run: `grep -E "core_dimensions|context_dimensions|phase name|scan|analyze|output_format|Select 2-4" agent-skills/red-team/SKILL.md`

Expected: all patterns match.

- [ ] **Step 4: Commit**

```bash
git add agent-skills/red-team/SKILL.md
git commit -m "Restructure red-team skill: tiered dimensions, two-phase prompt, eliminate compete mode

Core dimensions (always assess): correctness, completeness, clarity.
Context dimensions (select 2-4): parsimony, feasibility, resilience,
verifiability, data integrity, maintainability, security.

Two-phase prompt: scan (identify relevant dimensions) then analyze
(deep-dive on findings). XML-structured for clear boundaries.

Compete mode removed — two-phase prompt provides focused attention
without the cost of a second agent."
```

---

### Task 2: Update README.md

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Update "What it does" paragraph**

Match and replace:

**oldString:**
```
The **red-team** skill spawns one or two subagents to critically review any artifact with fresh eyes — specs, plans, designs, code, PRs, or documents. It returns a severity-ranked issue list across multiple review dimensions, with recommendations where there's high confidence and alternative notes where there isn't.
```

**newString:**
```
The **red-team** skill spawns a subagent to critically review any artifact with fresh eyes — specs, plans, designs, code, PRs, or documents. The subagent first scans for relevant review dimensions, then deep-dives on what matters. It returns a severity-ranked issue list with recommendations where there's high confidence and alternative notes where there isn't.
```

- [ ] **Step 2: Update "Why it's useful" section**

Match and replace:

**oldString:**
```
Adversarial review solves this by having a **different** agent do the review — one that didn't produce the work and has no investment in it. This eliminates the conflict of interest and produces dramatically better critiques.

The **compete mode** takes this further: two subagents review the same artifact independently, competing to find the most valuable issues. Competitive pressure incentivizes deeper analysis and surfaces problems that a single reviewer might miss.

Inspired by [Jesse Vincent's post on adversarial review](https://blog.fsck.com/2026/05/01/adversarial-review/), which showed that even a simple "look at this again with fresh eyes" produces startlingly good results — and that telling reviewers they're being evaluated against a competitor makes them work harder.
```

**newString:**
```
Adversarial review solves this by having a **different** agent do the review — one that didn't produce the work and has no investment in it. This eliminates the conflict of interest and produces dramatically better critiques.

The skill uses a **two-phase prompt**: the subagent first scans the artifact to identify which review dimensions are relevant, then deep-dives on what matters. This focused attention produces better critiques than spreading effort across a flat checklist.

Inspired by [Jesse Vincent's post on adversarial review](https://blog.fsck.com/2026/05/01/adversarial-review/), which showed that even a simple "look at this again with fresh eyes" produces startlingly good results.
```

- [ ] **Step 3: Update "How to use it" examples**

Match and replace:

**oldString:**
```
```
red-team this @docs/specs/my-feature.md
```

```
fresh eyes on this @plans/sprint-3.md
```

```
critique this plan, compete mode @docs/design.md
```
```

**newString:**
```
```
red-team this @docs/specs/my-feature.md
```

```
fresh eyes on this @plans/sprint-3.md
```

```
critique this for security and performance only @docs/api-design.md
```
```

- [ ] **Step 4: Replace Modes table and Review dimensions section**

Match and replace:

**oldString:**
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
```

**newString:**
```
#### Review dimensions

Dimensions are split into two tiers:

**Core dimensions** (always assessed):

1. **Correctness** — flawed reasoning, incorrect assumptions, internal contradictions
2. **Completeness** — missing requirements, unhandled cases, oversimplified descriptions
3. **Clarity** — ambiguous language, undefined terms, vague requirements

**Context dimensions** (subagent selects 2-4 based on artifact):

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
```

- [ ] **Step 5: Update Output format section**

Match and replace:

**oldString:**
```
#### Output format

Each issue includes:

- **Dimension tag** and **severity** (critical → minor)
- **Location** — the specific text, section, or line being critiqued
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared

This distinction matters: confident recommendations save time; flagged alternatives prevent premature commitment to a single approach.
```

**newString:**
```
#### How it works

The subagent works in two phases:

1. **Scan** — reads the artifact, identifies relevant context dimensions, notes which dimensions have issues
2. **Analyze** — deep-dives on each dimension with issues, producing the full issue report

Each issue includes:

- **Dimension** and **severity** (critical → minor)
- **Location** — the specific text, section, or line being critiqued
- **Description** of the problem
- **Recommendation** — only when there's high confidence in the solution
- **Alternative note** — when confidence is low or multiple solutions should be compared

This distinction matters: confident recommendations save time; flagged alternatives prevent premature commitment to a single approach.
```

- [ ] **Step 6: Verify — search for removed content**

Run: `grep -E "Modes|Compete|compete mode|depth/uniqueness|5 points|11 dimensions|10 orthogonal|two subagents|two reviewers" README.md`

Expected: no matches.

- [ ] **Step 7: Verify — search for new content**

Run: `grep -E "two-phase|Core dimensions|Context dimensions|scan|analyz|Select 2-4" README.md`

Expected: all patterns match.

- [ ] **Step 8: Commit**

```bash
git add README.md
git commit -m "Update README for restructured red-team skill: tiered dimensions, two-phase prompt, no compete mode"
```

---

### Task 3: Update CHANGELOG.md

**Files:**
- Modify: `CHANGELOG.md`

- [ ] **Step 1: Add changelog entry**

Match and replace the existing Unreleased section:

**oldString:**
```
## Unreleased

- Redesigned review dimensions: 10 orthogonal dimensions (correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, security) replacing the original 11 overlapping ones.
- Sharpened critique prompt: artifact-type weighting, citation requirement, no-issues-found guard.
- Replaced compete mode scoring: depth/uniqueness/actionability rubric (0-6) instead of arbitrary 5 points; structured convergence/divergence fusion instead of vague deduplication.
- Added repository management structure: released skills in `agent-skills/`, incubator for experiments, lifecycle documentation, MIT license.
- Documented existing `red-team` skill.
```

**newString:**
```
## Unreleased

- Restructured red-team skill: tiered dimensions (3 core always-assess + 7 context select-2-4), two-phase XML-structured prompt (scan then analyze), eliminated compete mode.
- Redesigned review dimensions: 10 orthogonal dimensions (correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, security) replacing the original 11 overlapping ones.
- Sharpened critique prompt: artifact-type weighting, citation requirement, no-issues-found guard.
- Replaced compete mode scoring: depth/uniqueness/actionability rubric (0-6) instead of arbitrary 5 points; structured convergence/divergence fusion instead of vague deduplication.
- Added repository management structure: released skills in `agent-skills/`, incubator for experiments, lifecycle documentation, MIT license.
- Documented existing `red-team` skill.
```

- [ ] **Step 2: Verify**

Run: `grep -E "tiered dimensions|two-phase|eliminated compete" CHANGELOG.md`

Expected: all three patterns match.

- [ ] **Step 3: Commit**

```bash
git add CHANGELOG.md
git commit -m "Add changelog entry for red-team restructuring"
```
