# caldiaworks-marketplace

Multi-agent skill marketplace supporting Claude Code, Antigravity, Gemini CLI, and Codex. Provides EARS for writing unambiguous specifications and USDM for structuring requirement documents.

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
```

## Available Skills

### ears

Convert vague specifications into clear, testable statements using EARS (Easy Approach to Requirements Syntax) patterns. Provides 6 templates that eliminate ambiguity through structured requirement writing.

**Use when:**
- Writing unambiguous specifications and requirements
- Clarifying vague or ambiguous specifications
- Creating testable, measurable requirement statements
- Working on technical specification documents

**Templates provided:**
- Ubiquitous (always-active behavior)
- Event-driven (triggered by discrete events)
- State-driven (sustained conditions)
- Unwanted behavior (error handling and edge cases)
- Optional feature (feature flag dependent)
- Complex (multi-condition behaviors)

### usdm

Convert ambiguous user requests into structured USDM requirements documents. Decomposes requirements into a Requirement → Reason → Description → Specification hierarchy with full traceability and verification.

**Use when:**
- Creating comprehensive requirements documents
- Decomposing high-level user needs into testable specifications
- Integrating requirements from GitHub Issues, Asana, or Jira
- Establishing requirement traceability and coverage
- Building structured requirement hierarchies

**Capabilities:**
- Multi-source input (direct text, GitHub Issues, Asana tasks, Jira tickets)
- 4-criterion systematic decomposition (temporal, structural, state-based, common)
- EARS pattern integration for specifications
- Markdown document and GitHub Issues output formats

## License

MIT
