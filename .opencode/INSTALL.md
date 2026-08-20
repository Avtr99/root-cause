# Installing Antidote for OpenCode

## Prerequisites

- [OpenCode.ai](https://opencode.ai) installed

## Installation

Add antidote to the `plugin` array in your `opencode.json` (global or project-level):

```json
{
  "plugin": ["antidote@git+https://github.com/Avtr99/antidote.git"]
}
```

Restart OpenCode. The plugin installs through OpenCode's plugin manager and
registers the antidote skill.

Verify by asking: "Use antidote" or "Audit for overcomplication"

OpenCode uses its own plugin install. If you also use Claude Code, Codex, or
another harness, install Antidote separately for each one.

## Migrating from the old symlink-based install

If you previously installed antidote using `git clone` and symlinks, remove the old setup:

```bash
# Remove old symlinks
rm -f ~/.config/opencode/plugins/antidote.js
rm -rf ~/.config/opencode/skills/antidote

# Optionally remove the cloned repo
rm -rf ~/.config/opencode/antidote

# Remove skills.paths from opencode.json if you added one for antidote
```

Then follow the installation steps above.

## Usage

Use OpenCode's native `skill` tool:

```
use skill tool to load antidote
```

## Updating

OpenCode installs Antidote through a git-backed package spec. Some OpenCode
and Bun versions pin that resolved git dependency in a lockfile or cache, so a
restart may not pick up the newest antidote commit. If updates do not appear,
clear OpenCode's package cache or reinstall the plugin.

To pin a specific version:

```json
{
  "plugin": ["antidote@git+https://github.com/Avtr99/antidote.git#v1.1.0"]
}
```

## Troubleshooting

### Plugin not loading

1. Check logs: `opencode run --print-logs "hello" 2>&1 | grep -i antidote`
2. Verify the plugin line in your `opencode.json`
3. Make sure you're running a recent version of OpenCode

### Windows install issues

Some Windows OpenCode builds have upstream installer issues with git-backed
plugin specs, including cache paths for `git+https` URLs and Bun not finding
`git.exe` even when it works in a normal terminal. If OpenCode cannot install
the plugin, try installing with system npm and pointing OpenCode at the local
package:

```powershell
npm install antidote@git+https://github.com/Avtr99/antidote.git --prefix "$HOME\.config\opencode"
```

Then use the installed package path in `opencode.json`:

```json
{
  "plugin": ["~/.config/opencode/node_modules/antidote"]
}
```

### Skills not found

1. Use `skill` tool to list what skills are discovered
2. Check that the plugin is loading (see above)

### Tool mapping

Skills speak in actions ("create a todo", "dispatch a subagent", "read a file"). On OpenCode these resolve to:

- "Create a todo" / "mark complete in todo list" → `todowrite`
- `Subagent (general-purpose):` template → `task` tool with `subagent_type: "general"` (or `"explore"` for codebase exploration)
- "Invoke a skill" → OpenCode's native `skill` tool
- "Read a file" → `read`
- "Create a file" / "edit a file" / "delete a file" → `apply_patch`
- "Run a shell command" → `bash`
- "Search file contents" / "find files by name" → `grep`, `glob`
- "Fetch a URL" → `webfetch`

## Getting Help

- Report issues: https://github.com/Avtr99/antidote/issues
- Full documentation: https://github.com/Avtr99/antidote/blob/main/skills/antidote/README.md
