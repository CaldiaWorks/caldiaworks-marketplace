---
name: git-workflow
version: 0.1.0
description: "Automate Git workflows (commit, push, PR, release, branch management) driven by a project-specific config file. Adapts to any repository's branch strategy, commit conventions, and release procedures. Use when: commit, push, PR, pull request, release, branch, git workflow, コミット, プッシュ, プルリクエスト, リリース, ブランチ. Also use when the user says 'commit and push', 'create a PR', 'release', 'clean up branches', or any Git workflow task — even if they don't mention this skill by name."
---

# Git Workflow

Automate Git workflows based on project-specific configuration. This skill reads a config file (`.claude/git-workflow.local.md`) to understand the project's branch strategy, commit conventions, and release procedures, then executes the appropriate workflow.

When no config file exists, guide the user through interactive setup to create one.

## Subcommands

The user invokes this skill with one of the following intents. Detect the intent from the user's message and execute the corresponding workflow.

| Intent | Trigger phrases | Workflow |
|--------|----------------|----------|
| **commit** | "commit", "コミット" | Commit Workflow |
| **pr** | "PR", "pull request", "push and PR", "プルリクエスト" | Push & PR Workflow |
| **release** | "release", "リリース" | Release Workflow |
| **branch** | "new branch", "feature branch", "ブランチ作成" | Branch Creation |
| **cleanup** | "clean up branches", "ブランチ整理" | Branch Cleanup |
| **setup** | "setup", "configure", "設定" | Interactive Config Setup |

If the intent is ambiguous, ask the user which workflow to run.

## Configuration

### Config File Location

Read the config file from `.claude/git-workflow.local.md` relative to the repository root.

### Config File Format

The config file uses YAML frontmatter. See `references/config-schema.md` for the full schema with all options and defaults.

```yaml
---
branches:
  integration: develop
  release: main
  use_feature_branches: true

commit:
  format: conventional    # "conventional" | "freeform"
  rules: []

pr:
  base_branch: develop
  body_sections:
    - summary
    - test_plan

release:
  manifests: []           # File paths with version fields to update
  tag_format: "v{version}"
  version_format: semver  # "semver" | "calver"
---
```

### Missing Config Handling

IF the config file does not exist at `.claude/git-workflow.local.md`, THEN run the Interactive Config Setup first. After setup completes, resume the originally requested workflow — do not stop after config creation.

Exception: Branch Cleanup does not use config values and can run without a config file.

## Workflows

### Interactive Config Setup

Run this when no config file exists or the user explicitly asks to configure.

1. Ask the user to specify the integration branch name (default: `develop`) and the release branch name (default: `main`).
2. Ask the user to specify the commit message format (`conventional` or `freeform`) and any project-specific rules.
3. Ask the user to specify manifest file paths to update during release, version format, and tagging convention.
4. Validate all inputs against expected types and values.
5. Write the config file to `.claude/git-workflow.local.md`.

Use `AskUserQuestion` with multiple choice options where possible to minimize typing.

### Commit Workflow

Execute in this order:

**1. Change analysis**

- Retrieve staged changes (`git diff --staged`), unstaged changes (`git diff`), and untracked files (`git status`).
- IF no changes exist, THEN inform the user and stop.
- Present a categorized summary of all changes.

**2. Secret detection**

- Scan the list of changed/untracked files for common secret patterns: `.env`, `.env.*`, files containing `credential`, `secret`, `token`, `password` in the name, and known secret file types.
- IF potential secrets are detected, THEN warn the user, list the suspicious files, and exclude them from staging unless the user explicitly overrides.

**3. File selection**

- Present the list of changed files to the user and ask which files to include in this commit.
- Default to all changed files, but allow the user to exclude specific files.

**4. Commit message generation**

- Read the 5 most recent commit messages to match the repository's existing style.
- Generate a commit message draft based on the diff content and the commit convention in the config.
- Present the message to the user and wait for approval or modification.

**5. Commit execution**

- Stage the user-selected files using `git add` with explicit file paths (never `git add -A` or `git add .`).
- Execute `git commit` with the approved message using a HEREDOC for formatting.
- Run `git status` to verify success.
- IF a pre-commit hook fails, THEN display the hook output, identify the issue, and suggest a fix. Do not use `--no-verify` to bypass hooks.

### Push & PR Workflow

Execute in this order:

**1. Pre-flight check**

- Check for uncommitted changes using `git status`. IF uncommitted changes exist, THEN warn the user and ask whether to commit first (run Commit Workflow), continue without committing, or stop.
- Check if a PR already exists for the current branch using `gh pr list --head <branch>`.
- IF a PR exists, THEN display the existing PR URL and ask the user whether to update it or stop.

**2. Push**

- Show the user what will be pushed (branch name, commit count, target remote).
- Confirm with the user before pushing.
- Push the current branch to `origin` with `-u` if no upstream is configured.
- IF push fails, THEN display the error and suggest corrective action.

**3. PR creation**

- Read the base branch from the config (default: integration branch).
- Collect all commits since divergence from the base branch using `git log <base>..HEAD`.
- Also run `git diff <base>...HEAD` to understand the full scope of changes.
- Generate a PR title (70 characters or fewer).
- Generate a PR body using this template:

```markdown
## Summary
- [1-3 bullet points summarizing the changes]

## Test plan
- [ ] [Testing checklist items]

[If branch name matches `feature/<number>-*` or `fix/<number>-*`, add:]
Closes #<number>
```

- Present the PR title and body to the user for confirmation before creating.
- Create the PR using `gh pr create`.
- Display the PR URL.

### Release Workflow

Execute in this order. Confirm with the user before each destructive step.

**1. Pre-flight check**

- Verify the current branch is the integration branch.
- Verify there are no uncommitted changes.
- Pull the latest changes on both integration and release branches.

**2. Merge**

- Switch to the release branch.
- Merge the integration branch.
- IF conflicts occur, THEN abort the merge, list conflicting files, and instruct the user to resolve manually.

**3. Version and tag**

- Determine the next version based on the config's version format and the most recent existing tag.
- Present the proposed version to the user and allow override.
- IF manifest files are configured, THEN update the version field in each file, stage, and commit.
- Create an annotated git tag.

**4. Push**

- Confirm with the user before pushing.
- Push the release branch and tags to origin.
- Switch back to the integration branch.

### Branch Creation

1. Read the integration branch name from the config.
2. Pull the latest changes on the integration branch.
3. Ask the user for a branch name.
4. Create the branch from the integration branch.

### Branch Cleanup

This workflow does not require config — it runs with git commands only.

1. Run `git fetch --prune`.
2. Identify local branches whose upstream is marked as `[gone]` using `git branch -vv` and filtering for `: gone]`.
3. Exclude the current branch and protected branches (integration branch, release branch if config exists) from candidates.
4. IF no gone branches remain, THEN inform the user and stop.
5. List the gone branches and ask the user for confirmation before deletion.
6. Delete confirmed branches using `git branch -d` (safe delete).
7. IF `git branch -d` fails for a branch (unmerged changes), THEN report the failure, explain why, and ask the user whether to force-delete with `git branch -D` or skip that branch.

## Safety Rules

- Never force-push unless the user explicitly requests it.
- Never commit files that may contain secrets (`.env`, credentials, tokens). Warn the user if detected.
- Always confirm before destructive operations (branch deletion, force push, release merge).
- Never modify git config.
- Use explicit file paths for staging, never `git add -A` or `git add .`.
- Always use HEREDOC for commit messages to preserve formatting.

## References

- `references/config-schema.md` — Full config file schema with all options and defaults
