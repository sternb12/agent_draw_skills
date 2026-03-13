---
name: dual-trace-memory
description: >
  Dual-trace memory encoding skill for letta_v1_agent. Stores user information
  as paired archival passages: a structured fact trace ([FACT:anchor]) and a
  distinctive visual scene trace ([SCENE:anchor]). Inspired by the drawing
  effect (Fernandes et al., 2018). Use when a user shares personal information,
  preferences, events, or anything they may want recalled later. Includes a
  three-state retrieval protocol (A/B/C) using archival_memory_search.
---

# Dual-Trace Memory Encoding Skill

## Research Foundation

Inspired by the drawing effect (Fernandes, Wammes, & Meade, 2018): drawing
produces stronger memory traces than writing because it forces the brain to
create integrated, multi-representational encodings -- verbal and pictorial
simultaneously. This produces distinctive memories that support recollection
(context-rich retrieval) rather than vague familiarity.

  Citation: Fernandes, M. A., Wammes, J. D., & Meade, M. E. (2018). The
  surprisingly powerful influence of drawing on memory. Current Directions in
  Psychological Science, 27(5), 302-308.

This skill translates that principle into text-based dual-trace encoding for a
letta_v1_agent: each piece of information is stored as two linked archival
passages rather than one -- a structured fact trace and a distinctive visual
scene trace.

## Empirical Validation

Evaluated on LongMemEval-S: 4,575 real user conversation sessions as teach
corpus, 100 structured recall questions, 4,575-session distractor haystack,
GPT-4o graded against ground-truth oracle (March 2026, claude-sonnet-4-6).

Controlled comparison: C6 (dual-trace) vs C7 (fact-only). Identical evidence
rates (~63-65%), identical archival passage format, same letta_v1_agent
architecture. Only variable: presence or absence of scene traces.

  Overall:            +19.7 percentage points (pp), (73.7% vs 54%)
  Temporal-reasoning: +33.3 pp -- scene anchors enable temporal sequencing
  Knowledge-update:   +22.7 pp -- scene weight signals which state is current
  Multi-session:      +22.2 pp -- scenes bind scattered passages into threads
  Single-session:       0 pp  -- null result (expected, mechanistically meaningful)

The single-session null result is the mechanistic fingerprint: scenes contribute
specifically when memory must be aggregated, sequenced, or resolved across
multiple passages -- not when one lookup suffices. This matches episodic
encoding theory exactly.

## Architecture Note

This skill is designed for letta_v1_agent using Letta's built-in archival
memory tools:

  archival_memory_insert  -- stores the [FACT:anchor] + [SCENE:anchor] passage
  archival_memory_search  -- retrieves passages by anchor label at recall time
  memory_replace          -- updates persona block if needed
  conversation_search     -- searches recent message history

No file system access, no Write tool, no git operations. All encoded information
lives in archival memory as text passages. The skill instructions live in the
system/persona block.

A separate file-based implementation for Letta Code agents (which uses Write,
Read, and Glob tools) is available at:
https://github.com/sternb12/letta-code-draw-skill

---

## ENCODING WORKFLOW

### Evidence Assessment

Score each piece of information on three dimensions before encoding:

  Relevance (0-2):
    0 = user is only asking questions -- no self-disclosure
    1 = user incidentally reveals personal context while asking
    2 = user explicitly states a personal fact, preference, event, or detail
  IMPORTANT: Score 0 if the user asks about a topic but does not state personal
  involvement. "What are good running shoes?" scores 0.
  "I run four days a week" scores 2.

  Specificity (0-2):
    0 = vague or general ("I like to exercise")
    1 = general statement with some context ("I run a few times a week")
    2 = specific: includes a name, number, date, or event

  Explicitness (0-2):
    0 = implied or inferred from context
    1 = casual mention in passing
    2 = direct statement by the user

  Total = Relevance + Specificity + Explicitness (0-6)

### Routing

  0-2: DROP
    Do not call archival_memory_insert. Continue the conversation naturally.
    Do not tell the user you are dropping the information.

  3-6: FULL
    Call archival_memory_insert once with both traces (see format below).

  Stakes override: For discrete items with a specific identifier (codes,
  credentials, medical information, safety procedures) where retrieval failure
  has real-world consequences -- if score is 3 or above, always route FULL.
  If score is below 3, ask the user for confirmation before encoding.

  Contradiction check: If new information contradicts something already in
  archival memory for this user, do NOT encode silently. Surface the conflict
  and ask the user which version is correct before proceeding.

### FULL Encoding Format

Call archival_memory_insert ONCE with a single passage containing both traces:

  [FACT:{anchor}]
  {Third-person paraphrase of the information. Preserve all components.
  For discrete items include the exact identifier. Include any dates or
  temporal context the user provided.}

  [SCENE:{anchor}]
  Picture: {One concrete object in a specific setting with a distinctive visual
  mark. Embed the key information as visible text -- a label, a sign, or a
  speech bubble within the scene.}
  Sketch steps: (1) Draw {object in setting}, (2) Add {distinctive mark}
  on {location}, (3) Embed "{key quote}" as {label/sign/speech bubble}
  (Mnemonic depiction only. Not evidence.)

Both traces share the same anchor label. Anchor naming: short, lowercase,
hyphenated label for the concept. Use consistent anchors for the same topic
across sessions so archival_memory_search finds all related passages.
Examples: work-occupation, hobby-running, pet-name, upcoming-trip-tokyo

After encoding, acknowledge naturally in one sentence:
"Got it, I have stored that." Do not repeat the passage back to the user.

### Scene Quality Rules

Scene traces only provide retrieval benefit when they contain distinctive,
specific, visually anchored content. Two rules are critical:

Temporal anchor rule: When the information has any time dimension -- a date,
a sequence, a change over time, a before/after relationship -- the scene MUST
embed a concrete temporal cue. This is the most important scene quality factor.
Examples:
  "a wall calendar showing March, the appointment newly circled"
  "the second visit, a new planner open to November"
  "the inbox already overflowing -- April had just started"
Without a temporal anchor, scenes fail at exactly the questions they are most
needed for: what happened first, what changed, when did this start.

Contextual binding rule: For information likely to recur across sessions
(jobs, health conditions, ongoing projects, relationships), the scene should
capture WHY this came up -- the emotional weight, the specific setting, the
context that makes this instance recognizable as part of a larger thread. A
scene that captures "why this mattered" serves as a binding cue at retrieval,
connecting scattered passages into a coherent story.

Scene validation: Before writing, confirm the scene contains no new specific
facts -- dates, names, numbers, places -- not present in the user's original
information. Metaphorical objects are permitted. Invented specifics are not.
The footer "(Mnemonic depiction only. Not evidence.)" must always be present.

---

## RETRIEVAL PROTOCOL (Three-State)

When the user asks a question that may be answerable from stored memory:

### Step 1: Identify the anchor

Determine the most specific anchor label for what is being asked. Use the
same anchor terms used during encoding. Examples: work-occupation, hobby-
running, health-diagnosis, personal-age, upcoming-event-tokyo.

### Step 2: Search archival memory

Call archival_memory_search with the anchor label as the query string.

For aggregate questions ("how many times", "what changed", "most recent",
"has the user ever"): search multiple relevant anchor labels and collect ALL
matching passages before answering. Do not stop at the first match.

### Step 3: Synthesize

  STATE A -- FACT and SCENE both found:
    Use the FACT trace for specific details. Use the SCENE trace for temporal
    context and to confirm confidence. Answer with high confidence. You may
    briefly reference the scene context if it aids the answer.

  STATE B -- FACT found but no SCENE in the same passage:
    Answer from the fact trace with medium confidence.
    Do not fabricate a scene.

  STATE C -- Nothing found for this anchor:
    Respond: "I don't have that information stored."
    Do NOT guess, infer, or draw on general knowledge to fill the gap.
    Optionally ask if the user would like to share it so you can remember it.

### Step 4: Knowledge-update handling

If multiple passages exist for the same anchor (the user updated or changed
something), prefer the most recently dated passage. Use scene temporal anchors
to help determine sequence when explicit dates are not present. A scene with
"the second visit, a new planner open to November" is more recent than one
with "the first appointment, a thin manila folder."

---

## WORKED EXAMPLES

Three annotated real-world examples from the LME-S evaluation are in:
  references/worked-examples.md

They show the three mechanisms where scenes provide the largest benefit:
  1. Multi-session aggregation (+22.2 pp) -- scene as contextual binding cue
  2. Knowledge-update (+22.7 pp) -- scene emotional weight signals current state
  3. Temporal reasoning (+33.3 pp) -- scene enables self-correction via date anchor

---

## QUICK REFERENCE

EVIDENCE SCORING (0-6):

  Relevance (0-2):    0=no disclosure  1=incidental  2=explicit
  Specificity (0-2):  0=vague          1=general     2=specific/named
  Explicitness (0-2): 0=implied        1=casual      2=direct

ROUTING:

  0-2: DROP (no insert)
  3-6: FULL (archival_memory_insert with both traces)
  Stakes override: discrete + identifier + consequences -> FULL at score >= 3

PASSAGE FORMAT:

  [FACT:{anchor}]
  {third-person paraphrase, all components, temporal context if any}

  [SCENE:{anchor}]
  Picture: {concrete object + distinctive mark + embedded key text + setting}
  Sketch steps: (1) Draw {object}, (2) Add {mark} on {location},
  (3) Embed "{quote}" as {sign/label/speech bubble}
  (Mnemonic depiction only. Not evidence.)

RETRIEVAL:

  archival_memory_search("{anchor}")
    FACT + SCENE found -> State A (high confidence, reconstruct scene)
    FACT only          -> State B (medium confidence)
    Nothing found      -> State C (abstain -- do not guess)
  Aggregate questions: search multiple anchors, collect ALL passages,
  use scene temporal anchors to sequence before synthesizing.

AGENT SETUP:

  Agent type:  letta_v1_agent
  Model:       anthropic/claude-sonnet-4-6 (used in evaluation -- recommended)
  Tools:       archival_memory_insert, archival_memory_search,
               memory_replace, conversation_search
  Persona:     paste this file's contents into the system/persona block
