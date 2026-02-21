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
```

## Available Skills

| Skill | Description |
|-------|-------------|
| **ideation** | Turn rough ideas into structured idea documents through collaborative dialogue with feasibility evaluation |
| **ears** | Write unambiguous specifications using EARS (Easy Approach to Requirements Syntax) patterns |
| **usdm** | Decompose requirements into structured USDM hierarchy (Requirement → Reason → Description → Specification) |
| **requirements-docx** | Export USDM/EARS requirements from Markdown to formatted Word (.docx) files |
| **en-explainer** | Explain English technical documents in Japanese with codebase context awareness |

## License

MIT
