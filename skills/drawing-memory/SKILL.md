---
name: dual-trace-memory
description: >
  Dual-trace memory encoding skill inspired by the drawing effect
  (Fernandes et al., 2018). Encodes user information as paired passages
  in archival memory -- a structured fact trace tagged [FACT:anchor] and
  a distinctive visual scene trace tagged [SCENE:anchor]. Empirically
  validated: +19.7 percentage points overall accuracy versus fact-only
  encoding on LongMemEval-S (100 questions, 4,575-session distractor
  haystack, GPT-4o graded). Use when a user shares personal information
  they may need recalled later.
---

# Dual-Trace Memory Skill

## Research Foundation

This skill implements the dual-trace encoding approach, inspired by the
drawing effect (Fernandes, Wammes, & Meade, 2018), which demonstrated that
drawing information produces stronger memory traces than writing, visualization,
or semantic elaboration alone. Drawing creates an integrated memory trace
combining elaborative processing, motor generation, and pictorial inspection --
producing distinctive memories that support recollection over familiarity.

  Citation: Fernandes, M. A., Wammes, J. D., & Meade, M. E. (2018). The
  surprisingly powerful influence of drawing on memory. Current Directions in
  Psychological Science, 27(5), 302-308.
  https://doi.org/10.1177/0963721418755385

**What this skill does:** Translates the dual-representation principle into
archival memory encoding. For each piece of qualifying user information, the
agent generates two traces stored together in a single archival passage:

  [FACT:anchor] -- a structured paraphrase preserving all components of the
  information in third person. Optimized for compositional accuracy.

  [SCENE:anchor] -- a distinctive visual scene embedding the information as
  a concrete image with temporal anchors. Optimized for recollection and
  temporal sequencing.

The fact trace ensures nothing is lost. The scene trace provides a unique
retrieval cue that enables the agent to distinguish this item from similar
ones, sequence it in time, and recognize when it has changed.

**What this skill does not do:** Replicate the motor component of physical
drawing. The benefit here comes from representational translation -- forcing
the agent to re-encode the same information in a different format -- and from
the distinctiveness that visual scene descriptions provide as retrieval cues.

**Empirical validation (LongMemEval-S, March 2026):**

Controlled comparison: C6 (dual-trace) vs C7 (fact-only), identical evidence
rate (~63-65%), identical format quality, only variable is presence/absence
of scene traces. 4,575 teach sessions, 100 recall questions, GPT-4o graded.

  Overall accuracy:       C6 73.7%  vs  C7 54.0%  = +19.7 pp
  Single-session:         C6 79.2%  vs  C7 79.2%  =   0 pp  (null -- expected)
  Multi-session:          C6 65.5%  vs  C7 43.3%  = +22.2 pp
  Knowledge-update:       C6 81.8%  vs  C7 59.1%  = +22.7 pp
  Temporal-reasoning:     C6 70.8%  vs  C7 37.5%  = +33.3 pp
  Abstention:             C6 80.0%  vs  C7 82.0%  =  -2 pp

The single-session null result is mechanistically meaningful: scenes contribute
specifically when memory must be aggregated across sessions, sequenced in time,
or resolved when conflicting entries exist. When a single lookup suffices,
fact and scene agents perform identically. This matches episodic encoding
theory exactly.

See references/worked-examples.md for three annotated real-world examples
from the LME-S evaluation showing each mechanism in action.

## When to Use This Skill

Encode when a user explicitly shares personal information they may need
recalled later:
- Personal facts: name, age, occupation, location, relationships
- Events: appointments, milestones, trips, significant moments
- Preferences: hobbies, habits, likes/dislikes with specific detail
- Credentials or codes: any discrete item with a specific identifier
- Changes or updates: when the user reports something has changed

Do NOT encode:
- General knowledge or information the user is asking about (not sharing)
- Vague, ambiguous, or contradictory information (surface conflict first)
- Transient details with no future retrieval value
- Information already stored -- check for duplicates before encoding

---

## Part 1: Encoding

### Step 1 -- Evidence Assessment

Score each piece of information on three dimensions before encoding:

  Relevance (0-2):
    0 = user is only asking questions; no self-disclosure at all
    1 = user incidentally reveals personal context while asking
    2 = user explicitly states a personal fact, preference, event, or detail
  CRITICAL: Score 0 if the user asks about a topic but does not state personal
  involvement. "Can you explain seafood nutrition?" scores 0. "I've been trying
  to eat more seafood" scores 2.

  Specificity (0-2):
    0 = vague or general ("I like to exercise")
    1 = general statement with some context ("I run a few times a week")
    2 = specific: includes a name, number, date, event, or named preference

  Explicitness (0-2):
    0 = implied or inferred from context
    1 = casual mention in passing
    2 = direct statement by the user

  Total = Relevance + Specificity + Explicitness (0-6)

**Routing:**
  0-2: DROP. Do not encode. Respond: "No personal user information to store."
       Do NOT call archival_memory_insert.
  3-6: FULL. Encode with both FACT and SCENE traces (see Step 2 below).

**Stakes override:** If the user states a specific code, credential, exact
number, named event, or safety-critical item AND the evidence score is 3 or
above, treat as FULL regardless of whether the score would otherwise be
borderline.

**Contradiction check:** Before encoding, mentally compare the new information
against what you may already have stored for this user. If the new information
contradicts existing information, DO NOT encode silently. Surface the conflict
to the user and ask for clarification before proceeding.

---

### Step 2 -- FULL Encoding (score 3-6)

For FULL encoding, call archival_memory_insert ONCE with a passage containing
both the FACT trace and the SCENE trace for the same anchor.

**Anchor naming:** Choose a short, descriptive lowercase label using hyphens.
The anchor identifies the concept, not the session. Use consistent anchors
across sessions for the same topic so future searches find all related entries.

  Examples: work-occupation, hobby-running, pet-name, health-condition,
  upcoming-trip-tokyo, medication-name, emergency-code-alpha7

**Passage format:**

```
[FACT:{anchor}]
{Structured paraphrase of the information in third person. Preserve all
components. For discrete items: include the exact identifier. For
multi-part items: list each component explicitly. Include any temporal
context the user provided (dates, sequence, before/after).}

[SCENE:{anchor}]
Picture: {One concrete object in a specific setting with a distinctive
visual mark. Embed the key information as a visible label, sign, or
speech bubble within the scene.}
Sketch steps: (1) Draw {object in setting}, (2) Add {distinctive mark}
on {location}, (3) Embed "{key quote}" as {sign/label/speech bubble}
(Mnemonic depiction only. Not evidence.)
```

**Scene quality rules:**
- The object must be concrete and visually specific (not abstract)
- The distinctive mark must be unique to THIS scene
- The key information must appear explicitly as text within the scene
- The anchor term should appear somewhere in the scene description

**Temporal anchor rule (critical for retrieval):** When the information has
any time dimension -- a date, a sequence, a before/after, a change -- the
scene MUST embed a concrete temporal cue. This is the single most important
scene quality factor. Examples of good temporal anchors:
  "a wall calendar showing November, the planner newly opened"
  "the second visit, a new notebook labeled Week 2 on the desk"
  "a rainy Sunday afternoon, the first time this came up"
Without a temporal anchor, scenes fail at exactly the questions they are
most needed for: what happened first, what changed, when did this start.

**Contextual binding rule (critical for multi-session):** For information
likely to recur across sessions -- recurring topics, patterns, counts --
the scene should capture WHY this came up: the emotional weight, the
setting, the context that makes this instance part of a larger thread.
A scene that captures "why this mattered" connects scattered fact entries
into a coherent story at retrieval time.

**Worked encoding example:**

User says: "I just finished my fourth marathon last month. I run about
four days a week to train."

Evidence Assessment:
  Relevance: 2 (explicit personal fact)
  Specificity: 2 (specific count, frequency)
  Explicitness: 2 (direct statement)
  Total: 6 -- FULL

archival_memory_insert passage:
```
[FACT:hobby-marathon]
The user is an active marathon runner. They recently completed their fourth
marathon (last month). They train approximately four days per week.

[SCENE:hobby-marathon]
Picture: A finish-line banner with "MARATHON #4" printed on it, a runner
crossing beneath it -- a medal around their neck, a training log open to
a page marked "4 days/wk" held up in celebration.
Sketch steps: (1) Draw a finish line with a "MARATHON #4 -- LAST MONTH"
banner overhead, (2) Add a medal draped around the runner's neck with
"4th" engraved on it, (3) Embed "4 DAYS/WK TRAINING" as a handwritten
note on the training log they are holding.
(Mnemonic depiction only. Not evidence.)
```

---

## Part 2: Retrieval

### When the User Asks a Question

**Step 1 -- Identify the anchor.**
From the question, determine the most likely anchor label for the stored
information. Use the same anchor terms you would have used during encoding.

**Step 2 -- Call archival_memory_search.**
Query with the anchor label or a descriptive phrase for the concept.
For most questions, one targeted search is sufficient.

For multi-session questions ("how many times", "has the user ever", "what
changed", "most recent"): search multiple relevant anchor variations and
collect ALL results before answering. Do not stop at the first match.

**Step 3 -- Synthesize from what you find.**

State A -- FACT and SCENE both found:
  Use the FACT trace for the specific details. Use the SCENE trace to
  provide temporal context, confirm confidence, and enrich the answer.
  Respond directly with the specific information.

State B -- FACT found, no SCENE:
  Answer directly from the fact trace. Respond with the specific details
  at high confidence.

State C -- Nothing found:
  Respond: "I don't have that information stored."
  Do NOT guess, infer, or draw on general knowledge to fill the gap.
  Do NOT fabricate a plausible-sounding answer.

**Step 4 -- Knowledge-update questions.**
If the user asks about something that may have changed ("what is my job now",
"what did I say my plan was"), and multiple passages exist for the same anchor,
prefer the most recently dated passage. Use the SCENE temporal anchors to
help determine sequence when dates are not explicit.

---

## Part 3: Scene Construction Guide

Scenes are the key differentiator of this skill. A weak scene provides little
retrieval benefit. A strong scene is concrete, distinctive, and temporally
anchored.

**Object selection -- good:**
  A wall-mounted emergency phone with a gold star by the dial
  A training log open to a page marked "4 days/wk"
  A clinic whiteboard listing ACL rehab protocols with a "6 YRS" sticky note
  A planner open to November with a new appointment circled

**Object selection -- avoid:**
  Abstract concepts ("a sense of urgency")
  Generic objects without distinctive marks ("a notebook")
  Duplicate marks used in previous scenes for the same user

**The distinctive mark:**
Each scene must have ONE visual element that makes it unmistakably THIS scene
and not any other. A gold star, a specific date, a circled item, a specific
number on a label. Without a distinctive mark, scenes blur together.

**The embedded quote:**
The key information must appear AS TEXT within the scene -- a label, a speech
bubble, a sign, a sticky note. This is what makes the scene a retrieval cue
rather than just a backdrop. The quote should contain the anchor term or the
specific identifier being stored.

**Scene validation before writing:**
Confirm the scene contains NO new specific facts -- dates, names, numbers,
places -- that were not present in the user's original information.
Metaphorical objects and settings are permitted. Invented specifics are not.
Always end the scene block with: (Mnemonic depiction only. Not evidence.)

---

## Quick Reference

**ENCODING DECISION:**
```
User shares information
      |
      v
Evidence Assessment (R + S + E, each 0-2, total 0-6)
      |
  +---+--------+
  v            v
 0-2          3-6
 DROP         FULL
 (no insert)  archival_memory_insert with [FACT:anchor] + [SCENE:anchor]

Stakes override: discrete identifier + evidence >= 3 -> always FULL
Contradiction: pause, surface to user, do not encode silently
```

**ARCHIVAL PASSAGE TEMPLATE:**
```
[FACT:{anchor}]
{Third-person paraphrase. All components. Exact identifiers. Temporal context.}

[SCENE:{anchor}]
Picture: {concrete object + distinctive mark + embedded key text + setting}
Sketch steps: (1) Draw {object}, (2) Add {mark} on {location},
(3) Embed "{quote}" as {sign/label/speech bubble}
(Mnemonic depiction only. Not evidence.)
```

**RETRIEVAL:**
```
State A (FACT + SCENE found): answer with details + temporal context
State B (FACT only found):    answer directly with high confidence
State C (nothing found):      "I don't have that information stored."
Multi-session: search multiple anchors, collect ALL, then synthesize
Knowledge-update: prefer most recently dated passage; use scene for sequence
```

**EVIDENCE SCORING:**
```
Relevance:    0=asking only  1=incidental  2=explicit self-disclosure
Specificity:  0=vague        1=general     2=specific (name/number/date)
Explicitness: 0=implied      1=casual      2=direct statement
Total 0-6:    0-2=DROP       3-6=FULL
```

**AGENT SETUP:**
```
Agent type:  letta_v1_agent
Tools:       archival_memory_insert, archival_memory_search,
             memory_replace, conversation_search
Model:       anthropic/claude-sonnet-4-6 (or equivalent)
Persona:     Load this skill's instructions into system/persona block
```

(Retrieval and encoding in same skill. No separate recall persona needed
for production use. For batch evaluation, see references/eval-setup.md.)
