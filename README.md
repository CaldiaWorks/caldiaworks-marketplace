# caldiaworks-marketplace

Multi-agent skill marketplace supporting Claude Code, Antigravity, Gemini CLI, and Codex.

Created by [@KoichiMatsudaMPL](https://github.com/KoichiMatsudaMPL).

## Installation

### Claude Code

Add this marketplace to Claude Code:

```
/plugin marketplace add CaldiaWorks/caldiaworks-marketplace
```

After adding the marketplace, browse available plugins:

```
/plugin install <plugin-name>@caldiaworks-marketplace
```

### npx skills

Install skills using the `npx skills` CLI:

```bash
npx skills add CaldiaWorks/caldiaworks-marketplace
```

Or install individual skills:

```bash
npx skills add CaldiaWorks/caldiaworks-marketplace -s ears
npx skills add CaldiaWorks/caldiaworks-marketplace -s usdm
npx skills add CaldiaWorks/caldiaworks-marketplace -s requirements-docx
npx skills add CaldiaWorks/caldiaworks-marketplace -s en-explainer
npx skills add CaldiaWorks/caldiaworks-marketplace -s ideation
npx skills add CaldiaWorks/caldiaworks-marketplace -s skill-creator
npx skills add CaldiaWorks/caldiaworks-marketplace -s skill-dev-workflow
npx skills add CaldiaWorks/caldiaworks-marketplace -s deep-research
npx skills add CaldiaWorks/caldiaworks-marketplace -s commit
npx skills add CaldiaWorks/caldiaworks-marketplace -s issue-create
npx skills add CaldiaWorks/caldiaworks-marketplace -s branch-create
npx skills add CaldiaWorks/caldiaworks-marketplace -s pull-request
npx skills add CaldiaWorks/caldiaworks-marketplace -s planning
npx skills add CaldiaWorks/caldiaworks-marketplace -s re-structure-analysis
```

## Available Skills

### Requirements Engineering

| Skill | Description |
|-------|-------------|
| **ideation** | Turn rough ideas into structured idea documents through collaborative dialogue with feasibility evaluation |
| **ears** | Write unambiguous specifications using EARS (Easy Approach to Requirements Syntax) patterns |
| **usdm** | Decompose requirements into structured USDM hierarchy (Requirement -> Reason -> Description -> Specification) |
| **requirements-docx** | Export USDM/EARS requirements from Markdown to formatted Word (.docx) files |

### Reverse Engineering (re-*)

8 skills organized as 4 Doer + 4 Critic pairs connected by a central `manifest.json` pipeline. Language-agnostic with optional language references (Python, TypeScript, C#, Java).

| Skill | Role | Description |
|-------|------|-------------|
| **re-structure-analysis** | Phase 1 Doer | Analyze codebase structure: entry points, dependencies, modules, components with file:line traceability |
| **re-verify-structure** | Phase 1 Critic | Verify structure map against source code for accuracy and completeness |
| **re-visualize-logic** | Phase 2 Doer | Convert source code into Mermaid flowcharts with line-number traceability |
| **re-verify-logic** | Phase 2 Critic | Verify logic diagrams against source code |
| **re-extract-requirements** | Phase 3 Doer | Extract EARS requirements from source code and logic diagrams |
| **re-verify-requirements** | Phase 3 Critic | Verify extracted requirements against source code |
| **re-generate-report** | Phase 4 Doer | Aggregate all artifacts into consolidated As-Is specification |
| **re-verify-report** | Phase 4 Critic | Verify consolidated report against Phase 1-3 artifacts |

### Git Workflow

| Skill | Description |
|-------|-------------|
| **commit** | Create focused git commits with change review, selective staging, and auto-drafted messages |
| **branch-create** | Create feature branches linked to issues with consistent naming conventions |
| **pull-request** | Create GitHub pull requests with readiness checks and auto-drafted descriptions |
| **issue-create** | Create GitHub Issues from user input or USDM requirements documents |
| **planning** | Analyze GitHub Issues, identify dependencies, and define work units with execution order |

### Development Tools

| Skill | Description |
|-------|-------------|
| **en-explainer** | Explain English technical documents in Japanese with codebase context awareness |
| **skill-creator** | Create new skills, improve existing skills, and measure skill performance |
| **skill-dev-workflow** | Orchestrate the full skill development lifecycle from idea to publication |
| **deep-research** | Conduct deep research through structured investigation design and multi-source synthesis |

## License

MIT
