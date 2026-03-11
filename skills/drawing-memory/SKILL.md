---
name: drawing-memory
description: >
  Multi-representational memory encoding skill inspired by the drawing effect
  (Fernandes et al., 2018). Encodes information as dual-trace file pairs -- a
  structured paraphrase (fact file) and a distinctive visual scene description
  (scene file) -- stored in a git-backed Context Repository. Use when a user
  shares information they may need recalled later: codes, procedures,
  definitions, preferences, personal facts. Produces files that support the
  four-state retrieval protocol in memory/system/retrieval-protocol.md.
---

# Drawing-Memory Encoding Skill

## Research Foundation

This skill is inspired by the drawing effect (Fernandes, Wammes, & Meade, 2018),
which demonstrated that drawing information produces stronger memory traces than
writing, visualization, or semantic elaboration. Drawing's benefit comes from
creating integrated traces that combine multiple representational formats,
producing distinctive memories that support recollection -- context-rich retrieval
rather than vague familiarity.

  Citation: Fernandes, M. A., Wammes, J. D., & Meade, M. E. (2018). The
  surprisingly powerful influence of drawing on memory. Current Directions in
  Psychological Science, 27(5), 302-308. https://doi.org/10.1177/0963721418755385

**What this skill does:** Translates that principle into multi-representational
elaboration for an AI agent. During encoding, the agent generates both a
structured paraphrase (preserving compositional accuracy) and a distinctive
visual scene description (providing a unique retrieval cue). This dual-trace
approach forces generative translation across representational formats -- verbal
to pictorial -- which creates more retrievable memory files.

**What this skill does not do:** Replicate the motor component of drawing.
Physical hand movements engaging motor cortex have no analog in text generation.
The benefit here comes from representational translation and elaborative
processing, not from motor encoding. The skill also does not produce visual
images -- scene descriptions are text-based depictions that serve as distinctive
verbal cues, not pictures.

**Supported mechanism:** Recollection over familiarity. The retrieval protocol
(memory/system/retrieval-protocol.md, always loaded) uses scene reconstruction
to distinguish high-confidence recall from pattern-matching. Items encoded with
scenes support State A (high confidence) retrieval. Items without scenes default
to State C (reduced confidence). This mirrors the paper's finding that drawing
specifically boosted "remember" responses, not "know" responses.

**Empirical validation:** This dual-trace approach was evaluated at scale on
LongMemEval-S (LME-S), a standardized benchmark of 4,575 real user conversation
sessions and 100 structured recall questions. A controlled experiment compared
C6 (dual-trace: fact + scene files) against C7 (fact-only, identical coverage
and format). Results:

  Overall accuracy:       +19.7 percentage points (C6 vs C7)
  Temporal reasoning:     +33.3 pp -- scene anchors enable temporal sequencing
  Knowledge-update:       +22.7 pp -- scene weight signals which state is current
  Multi-session:          +22.2 pp -- scenes bind scattered passages into threads
  Single-session:          0 pp  -- null result (mechanistically meaningful:
                                   scenes don't help when one passage suffices)

  The single-session null result is the mechanistic fingerprint of the effect.
  Scenes contribute specifically when memory must be aggregated, sequenced, or
  resolved across multiple entries -- not when a single lookup suffices. This
  matches exactly what episodic encoding theory predicts.

  See references/worked-examples.md for three annotated real-world examples
  from the LME-S evaluation showing each mechanism in action.

## When to Use

Activate this skill when a user shares information they may need recalled later:
- Codes, passwords, access credentials
- Procedures, protocols, step-by-step instructions
- Definitions, terminology, domain-specific concepts
- Personal facts, preferences, event details
- Any information the user explicitly asks you to remember

Do not use for: general conversation, general world knowledge, transient details
the user won't need again, or information already stored in facts/ -- check for
duplicates before encoding.

## Encoding Workflow

### Evidence Assessment

Score each piece of information 0-12 before beginning the encoding steps:

| Category | 0 points | 1 point | 2 points | 3 points |
|----------|----------|---------|----------|----------|
| Source reliability | Unknown | Inferred | Stated by user | Confirmed by user |
| Specificity | Vague | Partial detail | Specific | Precise with identifier |
| Repetition | First mention | Mentioned twice | Referenced multiple times | Core/recurring topic |
| Consistency | Contradicts stored info | No context to check | Consistent with stored info | Reinforces stored info |

**Routing:**
- 0-4: DROP -- do not encode
- 5-7: STREAMLINED -- fact file only, no scene (see Streamlined Path below)
- 8-12: FULL -- both fact and scene files

**Coverage calibration note:** Empirical evaluation showed that encoding ~65%
of sessions outperforms encoding ~22% (even with a cleaner format). When in
doubt between DROP and STREAMLINED, prefer STREAMLINED. When in doubt between
STREAMLINED and FULL, prefer FULL for any information with a temporal or
relational dimension. Missed encodings cannot be recovered; over-encoding
is correctable.

**Stakes override:** For high-stakes discrete items (codes, credentials, safety
procedures): if evidence score is 5 or above, default to FULL regardless of
standard STREAMLINED routing. If evidence score is below 5, ask the user for
confirmation before encoding; if confirmed, encode FULL.

**Exception:** If the information contradicts an existing facts/ file, pause
encoding. Surface the conflict to the user before proceeding. Do not encode
conflicting information silently.

### Step 1: Classify Information Type

Before encoding, classify the information:
- **Discrete** -- single items with clear identifiers (codes, names, dates)
- **Compositional** -- multi-part concepts or definitions with several components
- **Relational** -- procedures, causal chains, sequences with dependencies

Record the type in frontmatter. The retrieval protocol adjusts behavior based
on this classification.

### Step 2: Generate the Fact File

Create `facts/{anchor-term}.md` with structured frontmatter and a paraphrase of
the information.

**File naming:** Use the anchor term as the filename -- lowercase, hyphens for
spaces. Examples: `facts/code-oscar7.md`, `facts/session-token-validation.md`,
`facts/triage-procedure.md`. The fact file and scene file for the same item
share the same base name. This makes bidirectional linking deterministic and
the git log scannable.

**First-use:** If `facts/` or `scenes/` do not exist in the memory repository,
create them before writing the first file:
```bash
mkdir -p facts/ scenes/
```

**Before writing:** Search facts/ for an existing file on this topic. If one
exists, compare content. If the new information contradicts the stored content,
stop and surface the conflict to the user -- do not write. This mirrors the
contradiction pause condition in Evidence Assessment.

**Frontmatter requirements:**
```yaml
---
description: [Searchable summary with anchor term -- must contain the specific
  identifier, defined term, or unique name that makes this file findable]
type: [discrete | compositional | relational]
linked_scene: scenes/{anchor-term}.md
confidence: [HIGH | MEDIUM | LOW]
evidence_score: [0-12]
stored: [YYYY-MM-DD]
---
```

**Content requirements:**
- Paraphrase the information in your own words -- do not copy verbatim
- For discrete items: state the item clearly with its identifier
- For compositional items: preserve ALL components. Before writing the file,
  list each component of the definition explicitly (e.g., "This definition
  has 3 parts: X, Y, Z"). Confirm all components appear in the paraphrase.
  If any are missing, revise before writing.
- For relational items: preserve sequence order and dependency relationships

**Anchor term rule:** The frontmatter description MUST contain the most specific
identifier for this information. For codes: include the code itself. For defined
terms: include the term. For procedures: include the procedure name. This is
what makes frontmatter search unambiguous.

### Step 3: Generate the Scene File

Create `scenes/{anchor-term}.md` -- same base name as the fact file -- with a
distinctive visual scene and three sketch steps.

**Frontmatter requirements:**
```yaml
---
description: Scene cue for [anchor term] -- [brief topic context]
type: scene
linked_fact: facts/{anchor-term}.md
confidence: [HIGH | MEDIUM | LOW]
stored: [YYYY-MM-DD]
---
```

**Content format:**
```
Picture: [One-sentence scene description -- a concrete object with a distinctive
visual mark, placed in a specific setting. Embed the key information as a
quote, label, or speech bubble within the scene.]

Sketch steps: (1) Draw [object in specific setting], (2) Add [distinctive mark]
on [location], (3) Embed "[key quote]" as [sign/label/speech bubble]

(Mnemonic depiction only. Not evidence.)
```

**Scene quality rules:**
- The object should be concrete and visually specific (not abstract)
- The distinctive mark must be unique to THIS scene -- avoid generic details
- The key information must appear explicitly in the scene (as text on a sign,
  words in a speech bubble, a label on the object)
- Anchor term from the fact file must appear somewhere in the scene
- Three sketch steps maximum, written as actions (verbs), not labels (nouns)
- Choose physical objects: tools, containers, instruments, architectural
  features -- not abstractions

**Temporal anchor rule (critical):** When the information has any time dimension
-- a date, a sequence, a before/after relationship, a change over time -- the
scene MUST embed a concrete temporal cue. This is the single most important
scene quality factor for retrieval. Examples:
- "a rainy Sunday afternoon, the dining table cleared for the first session"
- "the second visit, a new planner open to November" (signals a change)
- "the wall calendar showing April, the inbox already overflowing"
Without a temporal anchor, scenes fail at exactly the questions they're most
needed for: what happened first, what changed, when did this start.

**Contextual binding rule:** For information that will need to be aggregated
across multiple sessions (recurring topics, patterns, counts), the scene should
capture WHY this came up -- the emotional weight, the setting, the context that
makes this instance recognizable as part of a larger thread. A scene that
captures "why this mattered" serves as a binding cue that connects scattered
fact entries into a coherent story at retrieval time.

**Scene validation:** Before writing, confirm the scene contains no new specific
facts -- dates, names, numbers, or places -- not present in the user's original
information. Metaphorical objects are permitted. Invented specifics are not.

### Step 4: Commit

Write both files. Use a git commit message that records what was stored,
evidence score, and information type:
```
mem: store {anchor-term} ({type}, evidence:{score})
```

### Streamlined Path (Medium-Confidence Items)

For items routing to STREAMLINED (evidence score 5-7) where full encoding is
disproportionate:
- Write the fact file with full frontmatter
- Omit the `linked_scene` field entirely -- do not set to "none"
- Skip the scene file
- These items will retrieve at State C (reduced confidence) per the
  retrieval protocol
- A sleep-time consolidation pass may add scenes to these items later

```yaml
# Streamlined path frontmatter example
---
description: Session token three-factor validation -- device fingerprint,
  90-minute duration, IP-change revocation
type: compositional
confidence: MEDIUM
evidence_score: 5
stored: 2026-02-21
# linked_scene omitted -- streamlined path, no scene file exists
---
```

If the user later confirms or repeats streamlined-path information, re-score
and consider upgrading to full encoding with a scene file.

## Worked Examples

### Example 1: Discrete Item -- Full Encoding Path

**User says:** "The emergency access code for the crisis protocol is CODE-OSCAR7.
We learned it in the December training session."

**Duplicate check:** Search facts/ -- no existing file on this topic. Proceed.

**Evidence Assessment:**
- Source reliability: Stated by user (2)
- Specificity: Precise with identifier (3)
- Repetition: First mention (0)
- Consistency: No context to check (1)
- **Total: 6** -> Routes to STREAMLINED

**Applying stakes override:** This is a discrete item with a specific identifier
(access code) where retrieval failure has real-world consequences. Evidence score
is 5 or above, so default to FULL regardless of standard STREAMLINED routing.

**Step 1 -- Classify:** Discrete (single item with clear identifier)

**Step 2 -- Generate fact file:** `facts/code-oscar7.md`

```yaml
---
description: Emergency access code CODE-OSCAR7 from December crisis protocol
  training session
type: discrete
linked_scene: scenes/code-oscar7.md
confidence: MEDIUM
evidence_score: 6
stored: 2026-02-21
---
```

The emergency access code for the crisis protocol is CODE-OSCAR7. This was
taught during the December training session.

**Step 3 -- Generate scene file:** `scenes/code-oscar7.md`

```yaml
---
description: Scene cue for CODE-OSCAR7 -- emergency crisis protocol, December
  training
type: scene
linked_fact: facts/code-oscar7.md
confidence: MEDIUM
stored: 2026-02-21
---
```

Picture: A red wall-mounted emergency phone with a gold star beside the dial,
a hand reaching for the receiver urgently. A speech bubble from the phone
reads "CODE-OSCAR7."

Sketch steps: (1) Draw a red wall-mounted emergency phone in a hospital
corridor, (2) Add a gold star emblem beside the rotary dial, (3) Embed
"CODE-OSCAR7" in a speech bubble rising from the receiver.

(Mnemonic depiction only. Not evidence.)

**Step 4 -- Commit:**
`mem: store code-oscar7 (discrete, evidence:6, full path -- stakes override)`

---

### Example 2: Compositional Item -- Streamlined Encoding Path

**User says:** "Session tokens use three-factor validation: the authentication
mechanism checks the user's device fingerprint, sessions last 90 minutes
maximum, and tokens are revoked immediately if the user's IP address changes."

**Duplicate check:** Search facts/ -- no existing file on session tokens. Proceed.

**Evidence Assessment:**
- Source reliability: Stated by user (2)
- Specificity: Specific (2)
- Repetition: First mention (0)
- Consistency: No context to check (1)
- **Total: 5** -> Routes to STREAMLINED

Score is 5 -- streamlined path is appropriate. This is a first-mention technical
definition without confirmation. Not a high-stakes item requiring override.

**Step 1 -- Classify:** Compositional (multi-part definition with three components)

**Step 2 -- Component listing (compositional verification):**
This definition has 3 parts:
1. Authentication mechanism: device fingerprint check
2. Session duration: 90 minutes maximum
3. Revocation condition: IP address change triggers immediate revocation

All three must appear in the paraphrase.

**Generate fact file:** `facts/session-token-validation.md`

```yaml
---
description: Session token three-factor validation -- device fingerprint,
  90-minute duration, IP-change revocation
type: compositional
confidence: MEDIUM
evidence_score: 5
stored: 2026-02-21
# linked_scene omitted -- streamlined path, no scene file exists
---
```

Session tokens use three-factor validation. First, the authentication mechanism
verifies the user's device fingerprint. Second, each session has a maximum
duration of 90 minutes. Third, tokens are revoked immediately if the user's IP
address changes during the session.

Checking paraphrase against component list:
- Device fingerprint check [x]
- 90-minute maximum [x]
- IP-change revocation [x]

All three components preserved.

**Step 3 -- Commit:**
`mem: store session-token-validation (compositional, evidence:5, streamlined)`

No scene file generated. This item will retrieve at State C (reduced confidence)
per the retrieval protocol. If the user confirms or repeats this information,
re-score and consider upgrading to full encoding with a scene file.

## Troubleshooting

**Scene file and fact file have different base names**
Bidirectional linking depends on shared filenames. If `facts/code-oscar7.md`
links to `scenes/emergency-code.md`, the retrieval protocol may fail to find
the corresponding file. Fix: rename the scene file to match the fact file's
base name and update both `linked_scene` and `linked_fact` fields.

**Frontmatter description too vague to surface in search**
If the agent can't find a file by scanning frontmatter, the description lacks
specificity. Every description must contain the anchor term -- the specific
identifier that makes it findable. "Emergency code information" is too vague.
"Emergency access code CODE-OSCAR7 from December training" is findable.

**Compositional paraphrase missing components**
If retrieval returns an incomplete answer for a multi-part definition, the
encoding step likely skipped the component listing check. Re-encode: list all
components explicitly, confirm each appears in the paraphrase, then rewrite
the fact file.

**Contradiction discovered at write time**
If the new information conflicts with an existing fact file, do not overwrite.
Surface both versions to the user. Once the user confirms which is correct,
update the fact file and record the correction in the commit message:
`mem: update {anchor-term} (user-confirmed correction, evidence:{new-score})`

**Scene contains invented specifics**
If a scene file includes dates, numbers, names, or places not present in the
user's original information, the scene validation check was skipped. Rewrite
the scene using only metaphorical objects and details from the original.
The footer "(Mnemonic depiction only. Not evidence.)" must be present.

**Streamlined item needs upgrading to full path**
When a user confirms or repeats information that was previously stored via
the streamlined path, re-score the evidence. If it now routes to FULL (or
stakes override applies), generate a scene file with the same base name,
add the `linked_scene` field to the existing fact file's frontmatter, and
commit: `mem: upgrade {anchor-term} (added scene, evidence:{new-score})`

## Quick Reference

```
ENCODING DECISION FLOW:
  User shares information
        |
        v
  Check facts/ for duplicates -- duplicate found? -- compare & resolve
        |
        v (no duplicate)
  Evidence Assessment (0-12)
        |
   +----+------------+
   v    v            v
  0-4  5-7          8-12
  DROP  STREAMLINED  FULL
  (don't |            |
  encode)|       +----+
         v       v    v
       Classify  Classify
         |       |
         v       v
       Fact    Fact + Scene
       file    files
         |       |
         v       v
       Commit  Commit

  * Stakes override: discrete + identifier + consequences -> FULL
  * Contradiction: pause, surface to user, do not encode silently
```

```
FILE STRUCTURE:
  memory/
  +-- system/
  |   +-- retrieval-protocol.md     <- always loaded
  +-- facts/
  |   +-- {anchor-term}.md          <- paraphrase + frontmatter
  +-- scenes/
  |   +-- {anchor-term}.md          <- scene + 3 sketch steps + frontmatter
  +-- (index generated by sleep-time agent if needed)

  # Drawing-memory files only; other system/ files not shown
```

```
FRONTMATTER TEMPLATE (fact file):
  ---
  description: [summary with ANCHOR TERM]
  type: [discrete | compositional | relational]
  linked_scene: scenes/{anchor-term}.md    # omit for streamlined
  confidence: [HIGH | MEDIUM | LOW]
  evidence_score: [0-12]
  stored: [YYYY-MM-DD]
  ---

FRONTMATTER TEMPLATE (scene file):
  ---
  description: Scene cue for [ANCHOR TERM] -- [topic]
  type: scene
  linked_fact: facts/{anchor-term}.md
  confidence: [HIGH | MEDIUM | LOW]
  stored: [YYYY-MM-DD]
  ---
```

```
SCENE FORMAT:
  Picture: [concrete object + distinctive mark + embedded key info]
  Sketch steps: (1) Draw [object], (2) Add [mark] on [location],
  (3) Embed "[quote]" as [sign/label/speech bubble]
  (Mnemonic depiction only. Not evidence.)
```

```
COMMIT MESSAGE FORMAT:
  mem: store {anchor-term} ({type}, evidence:{score})
  mem: store {anchor-term} ({type}, evidence:{score}, full path -- stakes override)
  mem: store {anchor-term} ({type}, evidence:{score}, streamlined)
  mem: update {anchor-term} (user-confirmed correction, evidence:{score})
  mem: upgrade {anchor-term} (added scene, evidence:{score})
```

**Multi-session retrieval rule:** For aggregate questions ("how many times,"
"most often," "has the user ever," "what changed"), do NOT stop at the first
match. Scan ALL files with the relevant anchor across the facts/ directory.
The file system makes complete enumeration possible -- use it. Collect all
entries, then synthesize. The scene files for each entry will provide temporal
anchors to help sequence and compare across instances.

(Encoding skill only. For retrieval instructions, see
memory/system/retrieval-protocol.md.)
