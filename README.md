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
npx skills add CaldiaWorks/caldiaworks-marketplace -s skill-create-workflow
npx skills add CaldiaWorks/caldiaworks-marketplace -s deep-research
npx skills add CaldiaWorks/caldiaworks-marketplace -s git-workflow
```

## Available Skills

| Skill | Description |
|-------|-------------|
| **ideation** | Turn rough ideas into structured idea documents through collaborative dialogue with feasibility evaluation |
| **ears** | Write unambiguous specifications using EARS (Easy Approach to Requirements Syntax) patterns |
| **usdm** | Decompose requirements into structured USDM hierarchy (Requirement → Reason → Description → Specification) |
| **requirements-docx** | Export USDM/EARS requirements from Markdown to formatted Word (.docx) files |
| **en-explainer** | Explain English technical documents in Japanese with codebase context awareness |
| **skill-creator** | Create new skills, improve existing skills, and measure skill performance |
| **skill-create-workflow** | Orchestrate the full skill development lifecycle from idea to publication |
| **deep-research** | Conduct deep research through structured investigation design: question decomposition, plan approval, iterative exploration, and synthesis |
| **git-workflow** | Automate Git workflows (commit, push, PR, release, branch management) driven by a project-specific config file |

## License

MIT
