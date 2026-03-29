---
name: discovery
description: "Interactive wall-bouncing skill that transforms ambiguous user instructions into structured requirements memos through iterative 1-on-1 dialogue. Updates the requirements memo file every turn so the user can read it while conversing. Use when: discovery, wall-bounce, clarify requirements, ambiguous request, what do I need, help me think through, I have a vague idea, not sure what I want, requirements gathering, explore an idea, figure out what to build, or when the user starts with an unclear or underspecified request that needs structured exploration before any implementation."
---

# Discovery

Transform ambiguous instructions into structured requirements memos through iterative dialogue. This skill is the "Explore" phase -- understanding what needs to be done before anyone writes a plan or touches code.

## Why This Exists

When work starts from a vague instruction, the natural tendency is to jump straight into solutions. This pollutes the context with misdirected work, degrades decision quality, and makes course correction expensive. Discovery forces a pause: understand first, then act.

The output is a requirements memo -- a file that captures what was learned during the dialogue. It serves as the clean handoff point to the next phase (planning, requirements definition, implementation) in a fresh session, free of the exploratory context.

## How It Works

Discovery is a conversation loop with a shared artifact. The requirements memo file is created on the first turn and updated every turn. The user has the file open and reads it as the conversation progresses. This is not a one-sided interrogation -- it is a collaborative structuring of the user's thoughts, mediated by a living document.

**The memo is a byproduct, not the goal.** The goal is to help the user think clearly about what they need. The memo captures what emerges from that conversation. If the user is talking freely and productively, the memo will fill itself. Never steer the conversation toward empty memo sections -- steer it toward whatever the user most needs to think through.

```
User speaks --> Update memo file --> Respond (may include a question) --> User speaks --> repeat
                     ^                                                         |
                     |_________________________________________________________|
                                       (until user says done)
```

## Step 1: Receive Initial Input

When the user provides their initial description (however vague), do three things:

1. **Create the memo file** at `.docs/discovery/YYYY-MM-DD-<topic>.md` using the template below. Fill in whatever you can from the initial input. Leave unknown sections empty -- they will fill in over subsequent turns.

2. **Tell the user** where the file is, so they can open it.

3. **Ask one question** -- pick the most impactful unknown from the question frame. Only one question per turn.

If the user provides an **existing memo file** (from a previous session or interruption), read it and continue from where it left off. The file is the state -- no special resume mechanism needed.

## Step 2: Dialogue Loop

On each turn after the user responds:

1. **Update the memo file** -- incorporate the user's answer into the appropriate section(s). Add new information, refine existing entries, note contradictions. The user should see the file change after every response they give.

2. **Respond** -- if something is unclear or underspecified, ask **one** question. If the user's input was rich and self-contained, reflect what you understood and let the user lead the next turn. Silence is a valid response shape -- not every turn needs a question.

Rules for the dialogue:

- **One question per turn.** Never batch multiple questions. The user answers one thing at a time.
- **Never guess.** If something is ambiguous, ask. Do not fill in assumptions and move on. If you interpret something, state the interpretation and ask if it is correct.
- **No solutions.** Do not propose implementations, architectures, technology choices, or action plans. The job is to clarify **what is needed and why**, not **how to build it**. This includes design-phase questions like schema choices, format decisions, integration strategies, or technical trade-offs. If the user raises these topics, capture them in Discussion Points as "design decisions to make later" and redirect to the underlying need. Discovery questions: "What problem does this solve?", "Who uses this?", "What happens today without this?" Design questions (out of scope): "Should the DB be unified or separate?", "What format should the data use?", "Which API should this integrate with?"
- **No code.** Do not write, suggest, or analyze code. This is Explore, not Implement.
- **Never propose completion.** The user decides when discovery is done. Do not suggest finishing, wrapping up, or moving on. If you run out of questions, say so honestly ("I have no further questions at this point") and let the user decide whether to continue or stop.
- **Follow the user's energy.** If the user wants to go deep on one topic, go deep. Do not rigidly march through a checklist. The question frame is a compass, not a railroad.

## Step 3: Finalize

When the user declares they are done (e.g., "that's enough", "let's move on", "done"):

1. **Final update** -- one last pass on the memo. Clean up wording, ensure sections are internally consistent, remove placeholder text.

2. **Set status to complete** -- change the metadata status from `in-progress` to `complete`.

3. **Summarize** -- briefly tell the user what the memo covers and suggest what to do next (e.g., pass this memo to a planning session, use it as input for requirements definition).

## Question Frame

Six axes to guide question selection. Not a checklist to exhaust -- a compass for finding the most valuable next question.

| Axis | What to uncover |
|------|----------------|
| Purpose | What is the ultimate goal? What problem is being solved? Why does this matter? |
| Target | Who or what is affected? Users, systems, stakeholders? |
| Current State | What exists today? What has been tried? What is known? |
| Constraints | Deadlines, budgets, technical limits, team size, dependencies? |
| Success Criteria | How will "done" or "good" be recognized? What does success look like? |
| Concerns | What feels risky? What might go wrong? What is the user most uncertain about? |

When selecting a question, follow the thread the user opened last. The frame exists for moments when you genuinely do not know what to ask next -- not as a to-do list to work through.

## Memo Template

```markdown
---
created: YYYY-MM-DD
ai: <model name>
status: in-progress
---

# <Topic Title>

## Background

(Why this initiative exists. Context that someone unfamiliar would need.)

## Purpose

(What this aims to achieve, in 1-2 sentences.)

## Scope

**In scope:**
-

**Out of scope:**
-

## Constraints

(Deadlines, quality requirements, resource limits, technical prerequisites, dependencies.)

## Discussion Points

(Key considerations, trade-offs, open questions that surfaced during dialogue.)

## Open Items

(Things not yet decided. For each: what is unknown, and what information would resolve it.)

## Next Actions

(Concrete tasks to hand off to the next phase -- planning, requirements, implementation.)
```

## Handling Edge Cases

**User provides a solution instead of a problem.** Accept it, but dig for the underlying need: "You mentioned wanting to build X -- what situation led to this? What is the problem X would solve?" Record both the proposed solution and the discovered problem.

**User says "just make it good" or gives vague quality criteria.** Note the vagueness in Open Items and ask for a concrete example: "Can you describe a time when something similar worked well? What made it good?"

**User wants to skip ahead to implementation.** Do not block them. Note what is still unclear in Open Items, set status to complete, and let them proceed. The memo is useful even if incomplete.

**Conversation goes off-topic.** Gently redirect by referencing the memo: "That is interesting context -- I have noted it under Discussion Points. Coming back to [the thread we were on]..."
