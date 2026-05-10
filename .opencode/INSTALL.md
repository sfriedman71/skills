# Installing Agent Skills for OpenCode

## Prerequisites

- [OpenCode.ai](https://opencode.ai) installed

## Installation

Add agent-skills to the `plugin` array in your `opencode.json` (global or project-level):

```json
{
  "plugin": ["agent-skills@git+https://github.com/sfriedman71/skills.git"]
}
```

Restart OpenCode. The plugin registers all skills automatically — no symlinks needed.

## Migrating from the old symlink-based install

If you previously installed using symlinks, remove the old setup:

```bash
rm -f ~/.config/opencode/skills/red-team
```

Then follow the installation steps above.

## Updating

OpenCode installs through a git-backed package spec. A restart may not pick up the newest commit. If updates do not appear, clear OpenCode's package cache or reinstall the plugin.

To pin a specific version:

```json
{
  "plugin": ["agent-skills@git+https://github.com/sfriedman71/skills.git#v0.1.0"]
}
```

## Troubleshooting

### Plugin not loading

1. Check logs: `opencode run --print-logs "hello" 2>&1 | grep -i agent-skills`
2. Verify the plugin line in your `opencode.json`
3. Make sure you're running a recent version of OpenCode

### Skills not found

1. Use `skill` tool to list what's discovered
2. Check that the plugin is loading (see above)
