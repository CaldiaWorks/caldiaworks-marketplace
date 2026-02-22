# Config File Schema

## File Location

`.claude/git-workflow.local.md` relative to the repository root.

## Format

YAML frontmatter in a markdown file. The markdown body is optional and can contain free-form workflow notes.

## Schema

```yaml
---
# Branch strategy (required)
branches:
  integration: develop       # Branch where features merge into
  release: main              # Production/release branch
  use_feature_branches: true # Whether to create feature branches for work

# Commit conventions (required)
commit:
  format: conventional       # "conventional" | "freeform"
                             #   conventional: type(scope): description
                             #   freeform: no enforced format
  rules:                     # Additional project-specific rules (list of strings)
    - "Do not include AI-related signatures"
    - "Keep subject line under 72 characters"

# PR conventions (optional, defaults shown)
pr:
  base_branch: develop       # Default PR target branch
  body_sections:             # Sections to include in PR body
    - summary                # Bullet-point summary of changes
    - test_plan              # Testing checklist

# Release procedures (optional)
release:
  manifests:                 # Files containing version fields to update
    - path: ".claude-plugin/plugin.json"
      field: "version"       # JSON field name
    - path: ".claude-plugin/marketplace.json"
      field: "metadata.version"  # Nested JSON field (dot notation)
  tag_format: "v{version}"   # Tag naming pattern ({version} is replaced)
  version_format: semver     # "semver" (x.y.z) | "calver" (YYYY.MM.DD)
---
```

## Field Reference

### branches

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `integration` | string | yes | `develop` | Branch where features are integrated |
| `release` | string | yes | `main` | Production/release branch |
| `use_feature_branches` | boolean | no | `true` | Whether to use feature branches |

### commit

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `format` | string | yes | `conventional` | `"conventional"` or `"freeform"` |
| `rules` | list of strings | no | `[]` | Additional commit message rules |

### pr

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `base_branch` | string | no | Same as `branches.integration` | Default PR target branch |
| `body_sections` | list of strings | no | `["summary", "test_plan"]` | Sections in PR body |

Supported body sections:
- `summary` — Bullet-point summary of changes
- `test_plan` — Testing checklist
- `breaking_changes` — Breaking change notes
- `references` — Related issue/PR links

### release

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `manifests` | list of objects | no | `[]` | Files with version fields |
| `manifests[].path` | string | yes | — | File path relative to repo root |
| `manifests[].field` | string | yes | — | Field name (dot notation for nested) |
| `tag_format` | string | no | `"v{version}"` | Tag naming pattern |
| `version_format` | string | no | `"semver"` | `"semver"` or `"calver"` |

## Example: Minimal Config

```yaml
---
branches:
  integration: develop
  release: main

commit:
  format: freeform
---
```

## Example: Full Config

```yaml
---
branches:
  integration: develop
  release: main
  use_feature_branches: true

commit:
  format: conventional
  rules:
    - "Do not include AI-related signatures"

pr:
  base_branch: develop
  body_sections:
    - summary
    - test_plan
    - references

release:
  manifests:
    - path: "package.json"
      field: "version"
    - path: ".claude-plugin/plugin.json"
      field: "version"
    - path: ".claude-plugin/marketplace.json"
      field: "metadata.version"
  tag_format: "v{version}"
  version_format: semver
---

## Notes

- Always update both plugin.json and marketplace.json together
- Both manifest files must have the same version number
```
