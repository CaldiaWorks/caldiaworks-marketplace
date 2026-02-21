# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**CaldiaWorks Skills** is a multi-platform skill distribution repository. Skills defined in this repository can be distributed across multiple AI agents and platforms via marketplace registries.

- **Repository**: `CaldiaWorks/caldiaworks-marketplace`
- **GitHub**: https://github.com/CaldiaWorks/caldiaworks-marketplace

## Supported Platforms

This repository provides skills for the following platforms:

| Platform | Project Path | User Path |
|----------|-------------|-----------|
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Antigravity | `.agent/skills/` | `~/.gemini/antigravity/skills/` |
| Gemini CLI | `.agents/skills/` | `~/.gemini/skills/` |
| Codex | `.agents/skills/` | `~/.codex/skills/` |

**Note**: Each platform has its own skill directory structure, allowing independent installation and management.

## Repository Structure

- `skills/` — Skill directories (each must contain SKILL.md)
- `.claude-plugin/` — Marketplace and plugin manifests

## Skill Development

### Creating or Updating a Skill

Use the `skill-creator` skill for all skill authoring and editing:

```
/skill-creator
```

This skill provides a guided workflow for:
- Creating new skills from scratch
- Improving existing skills
- Running evaluations to test skill performance
- Benchmarking skill performance with variance analysis

### Skill Structure

Each skill must be placed in a dedicated directory under `skills/<skill-name>/`:

1. **Create the directory**: `skills/<skill-name>/`
2. **Add SKILL.md**: Define the skill and its functionality
3. **Add README.md**: Document usage and examples
4. **Register in marketplace**: Add the skill path to `.claude-plugin/marketplace.json` in the `plugins[0].skills` array

### Example: Adding a Skill to Marketplace

Add the skill path as `"./skills/<skill-name>"` to the `plugins[0].skills` array in `.claude-plugin/marketplace.json`:

```json
{
  "name": "caldiaworks-marketplace",
  "owner": {
    "name": "CaldiaWorks"
  },
  "metadata": {
    "description": "CaldiaWorks plugin marketplace",
    "version": "0.1.2",
    "pluginRoot": "./"
  },
  "plugins": [
    {
      "name": "caldiaworks",
      "source": "./",
      "strict": false,
      "skills": [
        "./skills/ears",
        "./skills/usdm",
        "./skills/my-skill"
      ]
    }
  ]
}
```

**Important**:
- Do not modify `source`, `strict`, or other existing fields. Only append to the `skills` array.
- When changing `marketplace.json` or `plugin.json`, bump the `version` in `metadata.version`.

## Generated Artifacts

All generated files (Word documents, PDFs, images, etc.) must be saved to the `.docs/` directory. Do not output generated files to the project root or other directories.

- Output path: `.docs/<filename>`
- The `.docs/` directory is a staging area for generated artifacts. Whether to include them in version control is decided by the user.

## Git Workflow

### Branch Strategy

- **`main`**: Production/release branch (stable releases only)
- **`develop`**: Active development branch (integration branch for features)
- **Feature branches**: Branch from `develop`, merge via pull request

### Pull Request Workflow

1. Create feature branch from `develop`
2. Make changes and commit
3. Push to remote and open PR against `develop`
4. After approval and review, merge to `develop`
5. Release manager will merge `develop` → `main` for releases

### Commit Guidelines

- Write clear, descriptive commit messages
- Keep commits focused on single changes
- Follow conventional commit format when applicable
