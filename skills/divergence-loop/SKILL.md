---
name: divergence-loop
description: "Context-reset divergence brainstorming skill. Generates wild ideas through multiple rounds of context-isolated generation with extreme persona injection, then applies real-world constraints for structured convergence into multiple final ideas. Use when: brainstorm, diverge, divergence loop, idea generation, wild ideas, crazy ideas, generate ideas, brainstorming session, ideation divergence, think outside the box, or when the user wants to break out of conventional thinking patterns."
---

# Divergence Loop

A brainstorming skill that forces genuine creative divergence by exploiting context isolation between generation rounds, then bridges wild ideas back to reality through constraint-based convergence.

## Why Context Reset Matters

LLMs have a strong convergence bias -- given enough context, they drift toward safe, structured, "reasonable" outputs. Prompt-level instructions ("don't converge", "be wild") fail because the model's training rewards coherence over chaos. The only reliable way to maintain divergence across multiple rounds is **context isolation**: each generation round runs in a fresh execution context with zero memory of prior rounds. Files are the sole communication channel.

**How to achieve context isolation** depends on the platform. Use whatever mechanism provides a clean context with no carry-over from prior rounds (e.g., spawning a new agent, opening a new chat, or calling an API with an independent message history). The specific tool does not matter — what matters is that each round's generator has NO access to ideas from other rounds.

## Workflow Overview

```
User Theme --> [Divergence Loop] --> [Convergence] --> Multiple Final Ideas
                    |                      |
              N rounds of:           User constraints +
              Persona-injected       last round ideas
              context-isolated       = filtered output
              generation + mashup
              prompt extraction
```

## Phase 1: Divergence Loop

### Step 0: Receive Theme

Ask the user for their brainstorming theme. This can be anything: a product idea, a problem to solve, a domain to explore, a "what if" question. Accept it as-is without restructuring or clarifying -- ambiguity is fuel for divergence.

Also ask how many divergence rounds to run (default: 3). More rounds = wilder ideas, but diminishing returns beyond 5.

### Step 1: Select Personas

Read `references/persona-catalog.md` for the full persona list (80 entries across 8 categories).

Each round uses **3 personas** drawn from different category groups:
1. One from **extreme categories** (categories 1-5: extreme constraints, inhuman personas, scale anomalies, anti-patterns, inanimate concepts)
2. One from **famous people** (category 7)
3. One from **general/industry personas** (categories 6 or 8)

Selection rules:
- Never reuse a persona within the same session
- Each persona selection is random within its category group

### Step 2: Run Divergence Round (3 Context-Isolated Generators)

Run **3 independent generation tasks** -- one per persona. Each task runs in complete context isolation (no shared memory between generators) and produces 30+ ideas independently. Run them in parallel when the platform supports it.

Each generation task prompt must include:
1. The persona as a system-level framing (not "pretend to be X" -- instead, inject the persona's worldview as the generator's actual operating constraints)
2. The round's theme (see theme progression below)
3. Explicit instruction to generate 30+ ideas as a raw list, no categories, no evaluation, no ranking
4. Instruction to save output to `.docs/divergence-loop/<session-id>/round-<N>-<persona-label>-ideas.md`

**Theme progression across rounds:**
- **Round 1**: User's original theme
- **Rounds 2 to N-1** (intermediate): Mashup prompt generated from the previous round's ideas -- allows maximum divergence
- **Round N** (final): User's original theme again -- brings the diverged thinking back to the user's actual topic

This "go wild and come back" structure lets intermediate rounds explore freely without worrying about relevance, while the final round re-grounds everything in the user's intent. The personas in the final round have no memory of prior rounds, but the mashup-driven divergence from earlier rounds has already expanded the persona catalog's effective range.

Example prompt structure:

```
You are operating under these absolute constraints:
<persona worldview injected here>

Theme: <theme for this round>

Generate at least 30 ideas related to the theme. Rules:
- Raw list only. No categories, no grouping, no headers.
- No evaluation. Do not say "this could work" or "this is impractical".
- No explanation. Just the idea in 1-2 sentences max.
- Push past obvious ideas. The first 10 will be boring -- keep going.
- Contradictions are welcome. Ideas that conflict with each other are fine.

Save the complete list to: <output path>
```

After all 3 generators complete, do NOT read or summarize their outputs to the user. The ideas exist only in the files.

### Step 3: Extract Mashup Prompt (Isolated Context)

This step is skipped for the final round and the round before the final round (since the final round uses the original theme, not a mashup).

Run a NEW generation task in a fresh context (no carry-over from prior tasks). This task reads ALL 3 idea files from the current round and produces a mashup prompt for the next intermediate round.

The prompt:

```
Read all idea lists from this round:
- <round-N-extreme-ideas.md>
- <round-N-famous-ideas.md>
- <round-N-general-ideas.md>

Your job: create a NEW brainstorming theme by forcibly combining 3-5 of the most
conflicting or unexpected ideas picked ACROSS the different persona files.
The goal is to create a theme that is MORE specific and MORE strange than the original.

Rules:
- Pick ideas from at least 2 different persona files
- Pick ideas that seem incompatible and smash them together
- The output theme must be a single paragraph (3-5 sentences)
- Do not summarize or categorize the ideas -- just use them as raw material
- The theme should read like a bizarre but specific challenge, not a vague prompt
- Do not reference the original theme -- work only from the ideas in the files

Save the mashup prompt to: <output path for mashup prompt>
```

### Step 4: Loop

Repeat Steps 1-3 for the requested number of rounds. Each round:
- 3 fresh personas (one per category group, never repeated)
- 3 fresh context-isolated generators in parallel (no context from prior rounds)
- Theme follows the progression: original -> mashup -> ... -> mashup -> original

After all rounds complete, inform the user that Phase 1 is done and report only: how many rounds ran, which personas were used (all 3 per round). Do not summarize or show any ideas.

## Phase 2: Structured Convergence

### Step 5: Collect Constraints

Ask the user for real-world constraints. Examples:
- Budget limits
- Target audience
- Technical limitations
- Legal/regulatory requirements
- Timeline
- Team size
- Existing infrastructure

Accept whatever they provide. If they say "none", proceed without constraints.

### Step 6: Run Convergence (Isolated Context)

Run a final generation task in a fresh context. This task reads ONLY the last round's 3 idea files (the most diverged, most mutated ideas) and the user's constraints.

The prompt:

```
Read the idea lists from the final round:
- <last round extreme ideas path>
- <last round famous ideas path>
- <last round general ideas path>
Read the constraints: <user constraints>

Your job: produce 3-5 final idea proposals that bridge the wild ideas with reality.

IMPORTANT: Write the entire output in Japanese.

Rules:
- Each proposal must trace back to at least one idea from the files (cite it)
- Each proposal must address ALL stated constraints
- Proposals must be genuinely different from each other -- not variations of one idea
- For each proposal, include:
  - Title (sharp, memorable)
  - Core concept (2-3 sentences)
  - Why it's interesting (what makes it non-obvious)
  - Constraint compliance (how it meets each constraint)
  - Biggest risk (one honest weakness)
- Do NOT rank or recommend. Present all proposals as equally valid options.

Save the final proposals to: <output path>
```

### Step 7: Present Results

Read the convergence output file and present the final proposals to the user. This is the only point where ideas become visible to the user.

After presenting, the skill is complete. Do not auto-chain to `/ideation` or other requirements skills -- the user decides what to do next.

## Output Structure

All files are saved under `.docs/divergence-loop/<session-id>/`:

```
.docs/divergence-loop/<session-id>/
  session-log.md             # Session metadata and execution record (REQUIRED)
  round-1-extreme-ideas.md   # Ideas from Round 1 (original theme)
  round-1-famous-ideas.md    #
  round-1-general-ideas.md   #
  round-1-mashup.md          # Mashup prompt for Round 2
  round-2-extreme-ideas.md   # Ideas from Round 2 (mashup theme)
  round-2-famous-ideas.md    #
  round-2-general-ideas.md   #
  ...                        # (intermediate rounds have mashup.md)
  round-N-extreme-ideas.md   # Ideas from final round (original theme again)
  round-N-famous-ideas.md    #
  round-N-general-ideas.md   #
  convergence.md             # Final proposals in Japanese (the deliverable)
```

Session ID format: `YYYYMMDD-HHMMSS` (e.g., `20260322-143052`)

### Session Log (`session-log.md`)

This file is the **execution record** of the session. It MUST be created at the start and updated as each step completes. It serves as the single source of truth for reviewing whether the skill was executed correctly.

Required structure:

```markdown
# Session Log

## User Input
- **Theme**: <user's exact words, unmodified>
- **Rounds requested**: <number>

## Persona Selection

| Round | Extreme (Cat 1-5) | Famous (Cat 7) | General (Cat 6/8) |
|-------|-------------------|----------------|-------------------|
| 1     | #<N> <name>       | #<N> <name>    | #<N> <name>       |
| ...   | ...               | ...            | ...               |

## Round Execution

### Round 1
- **Theme type**: original
- **Theme content**: <user's original theme>
- **Idea files**: round-1-extreme-ideas.md, round-1-famous-ideas.md, round-1-general-ideas.md
- **Idea counts**: <extreme count>, <famous count>, <general count>
- **Mashup extracted**: yes / no (with reason if skipped)

### Round 2
- **Theme type**: mashup (from Round 1)
- **Theme content**: <mashup theme — copy the full text from the mashup file>
- **Idea files**: ...
- **Idea counts**: ...
- **Mashup extracted**: ...

### Round N (final)
- **Theme type**: original (return)
- **Theme content**: <user's original theme — must match Round 1>
- **Idea files**: ...
- **Idea counts**: ...
- **Mashup extracted**: no (final round)

## Phase 2: Convergence

### Constraints (user's exact words)
<paste the user's constraint input verbatim>

### Convergence Input
- **Files read**: <list the 3 idea files from the final round>
- **Output**: convergence.md
```

**Rules for session-log.md:**
- Create at the start of the session with User Input filled in
- Update after each round completes (do not batch-update at the end)
- Theme content for the final round MUST be identical to Round 1's theme content
- Idea counts must reflect the actual number of ideas in each file
- Constraints must be the user's exact words, not a summary or interpretation

## Execution Notes

- Each generation task MUST run in a fresh, isolated context. Never continue a prior context -- that defeats context isolation.
- If context isolation is not available on the platform, fall back to inline execution but warn the user that divergence quality may be reduced due to context bleed.
- The persona catalog has 80 entries. For sessions with more than 80 rounds (unlikely but possible), recycle personas from the least-recently-used pool.
- Do not show intermediate files to the user unless explicitly asked. The process is intentionally opaque to prevent premature convergence bias in the user's mind.
- The session-log.md file is mandatory. A session without a session log is considered incomplete regardless of whether the other files exist.
