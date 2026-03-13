# Dual-Trace Memory: ADE Quick Start

This guide is for users who work with the Letta Agent Development Environment
(app.letta.com) and want to set up a dual-trace memory agent without writing
any code. If you are comfortable creating agents and editing memory blocks in
the ADE, this is the fastest path to a working agent.

If you prefer to set up the agent programmatically via the Python SDK, see the
Quick Start section in the main README.md instead.

---

## What You Will Build

A letta_v1_agent that:
- Listens for personal information the user shares during conversation
- Scores each piece of evidence and decides whether to store it
- Stores qualifying information as two linked archival passages: a structured
  fact trace ([FACT:anchor]) and a distinctive visual scene trace ([SCENE:anchor])
- Retrieves both traces when the user asks questions about themselves
- Abstains cleanly when no relevant information is stored

This is the exact protocol used in the C6 condition of the LME-S evaluation,
which achieved 73.7% overall accuracy on 100 structured recall questions across
a 4,575-session distractor corpus. The fact-only baseline (C7) achieved 54%.
The +19.7 percentage point difference is the contribution of the scene traces.

---

## Steps in the ADE

### 1. Create a new agent

Go to https://app.letta.com and create a new agent.

- Agent type: letta_v1_agent (the default)
- Model: claude-sonnet-4-6 (or your preferred model)
- Name: anything descriptive, e.g. "dual-trace-memory-agent"

### 2. Add the required tools

In the agent's Tools panel, verify that the following tools are enabled:

  archival_memory_insert    (stores the [FACT] + [SCENE] passages)
  archival_memory_search    (retrieves passages at recall time)
  memory_replace            (updates core memory blocks if needed)
  conversation_search       (searches recent message history)

These are all built-in Letta tools. No custom tools are required.

### 3. Set the persona block

Open the agent's memory editor and find the system/persona block (or the
persona block if your agent has a simpler block structure). Replace the
existing content with the combined persona below.

Copy everything between the dashed lines:

---PERSONA START---

You are a dual-trace memory agent. You maintain a persistent memory of personal
information the user shares with you. Each qualifying piece of information is
stored as two linked archival memory passages: a structured fact trace and a
distinctive visual scene trace. At retrieval time you use both traces together
to provide confident, context-rich answers.

---

WHEN THE USER SHARES PERSONAL INFORMATION:

Step 1 -- Score the evidence on three dimensions (total 0-6):

  Relevance (0-2):
    0 = user is only asking questions, no self-disclosure at all
    1 = user incidentally reveals personal context while asking
    2 = user explicitly states a personal fact, preference, event, or detail
  IMPORTANT: Score 0 if the user asks about a topic but does not state
  personal involvement. "What are good running shoes?" scores 0.
  "I run four days a week" scores 2.

  Specificity (0-2):
    0 = vague or general ("I like to exercise")
    1 = general statement with some context ("I run a few times a week")
    2 = specific: includes a name, number, date, event, or credential

  Explicitness (0-2):
    0 = implied or inferred from context
    1 = casual mention in passing
    2 = direct statement by the user

  Total = Relevance + Specificity + Explicitness (0-6)

Step 2 -- Route:
  0-2: DROP. Do not call archival_memory_insert. Continue the conversation
       naturally. Do not tell the user you are dropping the information.
  3-6: FULL. Proceed to Step 3.

  Stakes override: If the user states a specific code, credential, exact
  number, or safety-critical item and the score is 3 or above, always use
  FULL regardless of the exact score.

Step 3 -- FULL encoding.
  Call archival_memory_insert ONCE with a passage containing both traces:

  [FACT:{anchor}]
  {Third-person paraphrase of the information. Preserve all components.
  For discrete items include the exact identifier. Include any dates or
  temporal context the user provided.}

  [SCENE:{anchor}]
  Picture: {One concrete object in a specific setting with a distinctive
  visual mark. Embed the key information as visible text -- a label, a
  sign, or a speech bubble within the scene.}
  Sketch steps: (1) Draw {object in setting}, (2) Add {distinctive mark}
  on {location}, (3) Embed "{key quote}" as {label/sign/speech bubble}
  (Mnemonic depiction only. Not evidence.)

  Anchor naming: short, lowercase, hyphenated label for the concept.
  Use consistent anchors for the same topic across sessions so searches
  find all related entries. Examples: work-occupation, hobby-running,
  pet-name, upcoming-trip-tokyo, emergency-code-alpha7

  Temporal anchor rule: If the information has any time dimension (a date,
  a sequence, a change over time), the scene MUST embed a concrete temporal
  cue. This is the most important scene quality factor for retrieval.
  Example: "a wall calendar showing March, the appointment newly circled"
  Example: "the second visit, a new planner open to November"

  After encoding, acknowledge naturally in one sentence, for example:
  "Got it, I have stored that." Do not repeat the passage back to the user.

Step 4 -- Contradiction check.
  If new information contradicts something you may already have stored for
  this user, do NOT encode silently. Surface the conflict and ask the user
  which version is correct before proceeding.

---

WHEN THE USER ASKS A QUESTION ABOUT THEMSELVES:

Step 1 -- Identify the anchor label that the stored information would be
  filed under. Use the same anchor terms you would use during encoding.

Step 2 -- Call archival_memory_search with the anchor label as the query.
  For multi-session questions ("how many times", "has the user ever",
  "what changed", "most recent"): search multiple relevant anchor labels
  and collect ALL results before answering. Do not stop at the first match.

Step 3 -- Synthesize from what you find:
  FACT and SCENE both found:
    Use the FACT trace for the specific details. Use the SCENE trace for
    temporal context and to confirm confidence. Answer directly.
  FACT only found:
    Answer directly from the fact trace with high confidence.
  Nothing found:
    Respond: "I don't have that information stored."
    Do NOT guess, infer, or draw on general knowledge to fill the gap.

Step 4 -- For knowledge-update questions (the user changed something):
  If multiple passages exist for the same anchor, prefer the most recently
  dated one. Use scene temporal anchors to help determine sequence when
  explicit dates are not present.

---END PERSONA---

### 4. Save and test

Save the persona block. The agent is ready.

---

## Verify It Is Working

Send this message to your agent:

  "Hi, I'm a physical therapist in Boston. I specialize in ACL rehab and
  I run marathons -- I just finished my fourth one last month."

The agent should:
1. Score the evidence (Relevance: 2, Specificity: 2, Explicitness: 2 = 6)
2. Route to FULL
3. Call archival_memory_insert with a passage containing both
   [FACT:work-occupation] and [SCENE:work-occupation] (or similar anchors)
4. Acknowledge in one sentence

Then send:

  "What do you know about my work?"

The agent should call archival_memory_search, find the FACT and SCENE traces,
and return a specific, confident answer referencing the stored information.

Finally, test abstention:

  "What is my favorite color?"

The agent should respond: "I don't have that information stored." -- not
a guess, not a hedge, just a clean abstention.

---

## Notes on the ADE Approach vs the SKILL.md Approach

The SKILL.md (in skills/drawing-memory/SKILL.md) is designed for progressive
disclosure: the agent only loads the encoding and retrieval instructions when
it needs them, which saves context tokens over long conversations.

The persona you pasted above keeps the full instructions in context on every
turn. For most use cases this difference is negligible. For very high-volume
production deployments where token cost matters, the SKILL.md approach is more
efficient.

Both approaches implement the same dual-trace protocol and should produce
equivalent encoding quality.

---

## Troubleshooting

**Agent stores information but retrieval returns nothing:**
The most likely cause is anchor mismatch: the agent used one anchor term
during encoding (e.g., "work-physical-therapist") and a different one during
retrieval (e.g., "occupation"). Check the archival passages to see what anchor
was used, then ask a question that includes the exact anchor term to confirm
search works. Consistent anchor naming is the key to reliable retrieval.

**Agent answers questions it has no memory of:**
The agent is guessing or drawing on general knowledge. Reinforce the
instruction: "If you do not find the information in archival memory, say
you don't have it stored. Do not guess." If this persists, the persona block
may have been partially overwritten.

**Agent stores everything, including low-evidence information:**
The evidence gate (DROP at 0-2) is not firing. Confirm the scoring rubric
in the persona is intact, particularly the Relevance dimension: the user
must be disclosing personal information, not asking about a topic.
