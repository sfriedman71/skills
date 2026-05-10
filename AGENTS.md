# Repository Instructions

- This repo stores reusable agent/LLM workflow skills for public sharing and portfolio use.
- Released skills live at `agent-skills/<name>/SKILL.md`; this is the only install-supported skill root.
- Experimental skills and process notes live under `incubator/<name>/`; do not list incubator skills in the root README released-skill table, and do not symlink incubator `SKILL.md` files into any skill loader.
- Skill files follow the OpenCode skill spec: YAML frontmatter with at least `name` and `description`, then the skill instructions.
- There is no package manifest, task runner, CI workflow, or automated test command in this repo. Do not assume `npm`, `pnpm`, or build/lint/test scripts exist.
- When adding or promoting a released skill, update the root `README.md` catalog table and matching skill section, and update `CHANGELOG.md` under `Unreleased`.
- Installation is by symlink from `agent-skills/*`; keep docs clear that `incubator/` is not an install target.
- The current released skill is `red-team`, triggered by phrases like `red-team`, `fresh eyes`, and `critique`; preserve its single-review and compete-mode behavior when editing it.
