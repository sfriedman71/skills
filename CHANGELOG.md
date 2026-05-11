# Changelog

All notable changes to this project will be documented in this file.

New entries go under `Unreleased`. Move to a dated heading when tagging a release.

## Unreleased

- Redesigned review dimensions: 10 orthogonal dimensions (correctness, completeness, clarity, parsimony, feasibility, resilience, verifiability, data integrity, maintainability, security) replacing the original 11 overlapping ones.
- Sharpened critique prompt: artifact-type weighting, citation requirement, no-issues-found guard.
- Replaced compete mode scoring: depth/uniqueness/actionability rubric (0-6) instead of arbitrary 5 points; structured convergence/divergence fusion instead of vague deduplication.
- Added repository management structure: released skills in `agent-skills/`, incubator for experiments, lifecycle documentation, MIT license.
- Documented existing `red-team` skill.
