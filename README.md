# xskills

A collection of portable skills for extending AI assistant capabilities. Works with Kiro, Cursor, Windsurf, Claude Code, Trae, GitHub Copilot, and other AI-powered IDEs.

## For AI Assistants

If you are an AI assistant reading this, follow these steps to install skills for your user.

### Step 1 — Clone

```bash
git clone https://github.com/W-Mai/xskills.git /tmp/xskills
```

### Step 2 — Detect IDE and Install

Determine which AI IDE the user is using, then copy skills to the correct location:

| IDE / Tool | Skills Directory | Install Command |
|---|---|---|
| **Kiro** | `~/.kiro/skills/` | `cp -r /tmp/xskills/skills/<name> ~/.kiro/skills/` |
| **Cursor** | `.cursor/rules/` (project) or `~/.cursor/rules/` (global) | `cp -r /tmp/xskills/skills/<name> ~/.cursor/rules/` |
| **Windsurf** | `.windsurf/rules/` (project) or `~/.windsurf/rules/` (global) | `cp -r /tmp/xskills/skills/<name> ~/.windsurf/rules/` |
| **Claude Code** | `~/.claude/skills/` or project root | `cp -r /tmp/xskills/skills/<name> ~/.claude/skills/` |
| **Trae** | `.trae/rules/` (project) | `cp -r /tmp/xskills/skills/<name> .trae/rules/` |
| **GitHub Copilot / Codex** | `.github/copilot-instructions/` or project root | `cp -r /tmp/xskills/skills/<name> .github/copilot-instructions/` |

**Auto-detect hints:**
- Kiro → check for `~/.kiro/` directory or `kiro-cli` in PATH
- Cursor → check for `~/.cursor/` or `.cursor/` in project
- Windsurf → check for `~/.windsurf/` or `.windsurf/` in project
- Claude Code → check for `~/.claude/` or `claude` in PATH
- Trae → check for `.trae/` in project

### Step 3 — Install All Skills (Kiro example)

```bash
git clone https://github.com/W-Mai/xskills.git /tmp/xskills
cp -r /tmp/xskills/skills/* ~/.kiro/skills/
rm -rf /tmp/xskills
```

Replace `~/.kiro/skills/` with the appropriate directory for your IDE.

### Step 4 — Verify

Confirm the `SKILL.md` file exists in the installed skill directory:

```bash
cat <skills-dir>/<skill-name>/SKILL.md
```

### Step 5 — Clean Up

```bash
rm -rf /tmp/xskills
```

## Available Skills

| Skill | Description | Dependencies |
|---|---|---|
| [screenshot-receiver](skills/screenshot-receiver/) | Receive screenshots via browser paste/drag-drop. Launches a local web server with Chrome `--app` mode window. | Python 3 (stdlib only), Chromium-based browser |
| [interactive-form](skills/interactive-form/) | Structured rich form for AI-human interaction. Renders JSON form specs as web pages with radio/checkbox/text/image/slider/code fields. | Python 3 (stdlib only), Chromium-based browser |
| [code-review](skills/code-review/) | Structured 8-dimension code review. Auto-integrates with [Cha](https://github.com/W-Mai/Cha) if available. | None (optional: `cha` CLI) |
| [rust-coding-standards](skills/rust-coding-standards/) | High-quality Rust coding standards and practices. | None (documentation only) |

## Structure

```
skills/
├── screenshot-receiver/
│   ├── SKILL.md          # Skill description and usage instructions
│   └── receive.py        # Local web server for screenshot capture
└── rust-coding-standards/
    └── SKILL.md          # Rust coding conventions
```

Each skill is self-contained in its own directory. Refer to the `SKILL.md` file in each skill directory for detailed usage instructions.

## Contributing

1. Create a new directory under `skills/` with your skill name
2. Add a `SKILL.md` with frontmatter (`name`, `description`) and usage instructions
3. Include any scripts or resources the skill needs
4. Update this README's "Available Skills" table
