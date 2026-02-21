---
name: skill-create-workflow
description: >-
  Orchestrate the full skill development lifecycle from idea to publication.
  Guides through ideation, requirements definition, skill implementation, and marketplace registration.
  Use when: "create a new skill end-to-end", "skill development workflow", "build and publish a skill",
  "スキル開発ワークフロー", "スキルを作って公開", "新しいスキルを開発".
---

# Skill Development Workflow

Orchestrate the full lifecycle of creating a skill for the CaldiaWorks marketplace.

This workflow combines general-purpose skills (`ideation`, `usdm`) with skill-specific tools (`skill-creator`) to guide the user from a rough idea to a published skill.

## Workflow Steps

Execute these steps in order. Each step produces output that feeds into the next.

### Step 1: Ideation

Invoke the `ideation` skill to explore and refine the skill idea.

- Input: User's rough idea or problem description
- Output: Idea document at `.docs/ideas/YYYY-MM-DD-<topic>.md`
- The idea document captures What, Why, Scope, Stakeholders, and Approaches

Ask the user: "Do you want to proceed to requirements definition, or keep this as an idea draft?"

- If the user wants to proceed → continue to Step 2
- If the user wants to stop → end the workflow here

### Step 2: Requirements Definition

Invoke the `usdm` skill with the idea document as input.

- Input: The idea document from Step 1
- Output: USDM requirements document at `.docs/`
- The requirements document decomposes the idea into Requirement → Reason → Description → Specification hierarchy

Ask the user: "Requirements are ready. Proceed to implementation?"

### Step 3: Implementation

Invoke the `skill-creator` skill to implement the skill.

- Input: The USDM requirements document from Step 2
- Follow the skill-creator process: understand → plan → initialize → edit → package
- Output: A complete skill directory under `skills/<skill-name>/`

The skill must include:
- `SKILL.md` with proper frontmatter
- Any necessary bundled resources (scripts/, references/, assets/)
- `.claude-plugin/plugin.json`

### Step 4: Marketplace Registration

Register the skill in the marketplace manifest.

1. Verify `.claude-plugin/plugin.json` exists in the skill directory:

```json
{
  "name": "<skill-name>",
  "version": "0.1.0",
  "description": "<Short description>",
  "skills": ["."]
}
```

2. Add the skill path to `.claude-plugin/marketplace.json` in the `plugins[0].skills` array.

3. Bump `metadata.version` in `marketplace.json`.

4. Report completion to the user with the registered skill path.
