# Skill Lifecycle

## Repository Layout

Released skills live in `skills/<name>/SKILL.md`.

Experimental skills live in `incubator/<name>/`. They may include draft `SKILL.md` files, scenarios, notes, and open questions, but they are not install targets and must not be symlinked into skill loaders.

## Authoring Standards

- Use lowercase hyphenated skill names.
- Every released skill must have a `SKILL.md`.
- `SKILL.md` frontmatter must include `name` and `description`.
- The description should explain when an agent should load the skill, not summarize the workflow.
- Keep instructions compact, operational, and specific to agent behavior.
- Put large references or reusable scripts beside the skill only when necessary.

## Evaluation

Evaluate skills with realistic usage scenarios before release.

Useful scenarios test:

- Whether an agent knows when to invoke the skill.
- Whether the instructions produce the intended behavior.
- Whether pressure, ambiguity, or conflicting goals cause failure.
- Whether the skill contains enough context without becoming bloated.

Record draft scenarios and notable failures in `incubator/<name>/README.md`.

## Promotion Checklist

Before moving a skill to `skills/`:

- Frontmatter has valid `name` and `description`.
- Description starts with a clear use trigger.
- Instructions are compact and actionable.
- Scenarios or evaluation notes exist.
- Root `README.md` catalog table is updated.
- Root `README.md` matching skill section is added or updated.
- Install instructions still point only to `skills/*`.
- `CHANGELOG.md` updated under `Unreleased`.

## Maintenance

- Keep the root README focused on released skills.
- Keep incubator content clearly marked as experimental.
- Update `CHANGELOG.md` for released skills and notable lifecycle changes.
- Move `Unreleased` entries to a dated heading when tagging a release.
- Archive or remove stale experiments when they no longer represent useful process.

## Public Release Readiness

Before publishing or sharing the repository:

- Verify install commands work from a fresh checkout.
- Check GitHub repo description, topics, and visibility.
- Scan for secrets, private paths, credentials, or sensitive content.
- Verify Markdown rendering, internal links, and heading anchors.
