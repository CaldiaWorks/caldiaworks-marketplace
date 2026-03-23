---
name: divergence-loop
description: "Context-reset divergence brainstorming skill. Generates wild ideas through multiple rounds of subagent execution with extreme persona injection, then applies real-world constraints for structured convergence into multiple final ideas. Use when: brainstorm, diverge, divergence loop, idea generation, wild ideas, crazy ideas, generate ideas, brainstorming session, ideation divergence, think outside the box, or when the user wants to break out of conventional thinking patterns."
---

# Divergence Loop

A brainstorming skill that forces genuine creative divergence by exploiting context isolation between subagent rounds, then bridges wild ideas back to reality through constraint-based convergence.

## Why Context Reset Matters

LLMs have a strong convergence bias -- given enough context, they drift toward safe, structured, "reasonable" outputs. Prompt-level instructions ("don't converge", "be wild") fail because the model's training rewards coherence over chaos. The only reliable way to maintain divergence across multiple rounds is **physical context isolation**: each generation round runs in a fresh subagent with zero memory of prior rounds. Files are the sole communication channel.

## Workflow Overview

```
User Theme --> [Divergence Loop] --> [Convergence] --> Multiple Final Ideas
                    |                      |
              N rounds of:           User constraints +
              Persona-injected       last round ideas
              subagent generation    = filtered output
              + mashup prompt
              extraction
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

### Step 2: Run Divergence Round (3 Parallel Subagents)

Spawn **3 subagents in parallel** -- one per persona. Each subagent runs in complete context isolation and generates 30+ ideas independently.

Each subagent prompt must include:
1. The persona as a system-level framing (not "pretend to be X" -- instead, inject the persona's worldview as the subagent's actual operating constraints)
2. The round's theme (see theme progression below)
3. Explicit instruction to generate 30+ ideas as a raw list, no categories, no evaluation, no ranking
4. Instruction to save output to `.docs/divergence-loop/<session-id>/round-<N>-<persona-label>-ideas.md`

**Theme progression across rounds:**
- **Round 1**: User's original theme
- **Rounds 2 to N-1** (intermediate): Mashup prompt generated from the previous round's ideas -- allows maximum divergence
- **Round N** (final): User's original theme again -- brings the diverged thinking back to the user's actual topic

This "go wild and come back" structure lets intermediate rounds explore freely without worrying about relevance, while the final round re-grounds everything in the user's intent. The personas in the final round have no memory of prior rounds, but the mashup-driven divergence from earlier rounds has already expanded the persona catalog's effective range.

Example subagent prompt structure:

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

After all 3 subagents complete, do NOT read or summarize their outputs to the user. The ideas exist only in the files.

### Step 3: Extract Mashup Prompt (Subagent)

This step is skipped for the final round and the round before the final round (since the final round uses the original theme, not a mashup).

Spawn a NEW subagent (fresh context). This subagent reads ALL 3 idea files from the current round and produces a mashup prompt for the next intermediate round.

The subagent prompt:

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
- 3 fresh subagents in parallel (no context from prior rounds)
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

### Step 6: Run Convergence (Subagent)

Spawn a final subagent with fresh context. This subagent reads ONLY the last round's 3 idea files (the most diverged, most mutated ideas) and the user's constraints.

The subagent prompt:

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

## Execution Notes

- Each subagent MUST use the Agent tool with a fresh spawn. Do not use SendMessage to continue a prior agent -- that defeats context isolation.
- If subagent spawning fails, fall back to inline execution but warn the user that divergence quality may be reduced due to context bleed.
- The persona catalog has 80 entries. For sessions with more than 80 rounds (unlikely but possible), recycle personas from the least-recently-used pool.
- Do not show intermediate files to the user unless explicitly asked. The process is intentionally opaque to prevent premature convergence bias in the user's mind.
