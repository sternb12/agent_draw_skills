---
name: drawing-memory
description: Store facts with evidence-backed validation using the "drawing effect" - an integrated process of elaboration, motor generation, and pictorial inspection that improves later recall. Use when user shares information to remember (preferences, dates, codes, procedures, technical details) and there are concrete quotes or context to support storage. This skill guides you through creating rich memory traces that support recollection, not just familiarity.
---

# Drawing Memory Skill

## Research Foundation

This skill implements the "drawing effect" from cognitive science research (Fernandes et al. 2018). Drawing beats writing, visualization alone, or viewing pictures because it creates an **integrated memory trace** with three components:

1. **Elaborative/Semantic processing** - Understanding and transforming the information
2. **Motor component** - Actively generating/doing, not just reading
3. **Pictorial inspection** - Visual checking and verification

**Critical insight**: The benefit shows at **retrieval**, not just encoding. Drawing improves **recollection** (remembering context and details), not just familiarity (feeling you've seen something).

**AI translation**: You can't draw, but you CAN:
- Transform information (paraphrase)
- Generate instructions (motor analogue - sketch steps YOU create)
- Create visual descriptions (scene text)
- Use scenes as retrieval cues (context reinstatement)

**The key**: YOU must do the generative work. Auto-generated content won't help memory.

---

## When to Use This Skill

**Apply when:**
- User shares factual information (preferences, dates, codes, procedures, technical details)
- Information has supporting evidence (quotes, context, timestamps)
- Memory should be durable and retrievable later
- Confabulation prevention matters (critical facts, technical details)
- You want to create a richer memory trace than simple storage

**Do NOT apply when:**
- Casual conversation without facts to store
- Information already in memory (check first!)
- User explicitly asks not to remember
- No evidence available (no quotes, no context)

---

## Core Workflow

This skill has **TWO critical phases**: ENCODING (storing) and RETRIEVAL (recalling).

### ENCODING PHASE (When Storing Information)

**Follow these steps in order. Don't skip the motor component!**

#### Step 1: Extract Evidence Naturally

From the conversation, identify:
- **Verbatim quotes**: What the user actually said (aim for 2-3 quotes)
- **Context**: When/where/why this was shared
- **Source**: "User conversation on [date]"
- **Timestamp**: Current time (ISO 8601 format: YYYY-MM-DDTHH:MM:SSZ)

**No JSON formatting required!** Just identify these elements from the natural conversation.

**Example:**
```
User: "In the Dec 12th training, the facilitator said CODE-OSCAR7 is the emergency code. This is part of the crisis management protocol."

Evidence you identified:
- Quote 1: "facilitator said CODE-OSCAR7 is the emergency code" (8 words)
- Quote 2: "This is part of the crisis management protocol" (9 words)
- Context: Training session on December 12th about crisis management
- Source: User conversation on January 6, 2026
- Timestamp: 2026-01-06T02:30:15Z
```

**Tips:**
- Look for complete thoughts, not just keywords
- Prefer longer quotes (12+ words) when possible
- Include context clues (dates, locations, why mentioned)

#### Step 2: Assess Evidence Quality (Flexible)

Score the evidence 0-10 using this formula:

**Quote count:**
- 1 quote = 2 points
- 2 quotes = 4 points
- 3+ quotes = 6 points

**Quote length:**
- Total words ÷ 12 (up to 4 points)
- Example: 36 words total = 3 points

**Metadata:**
- Has source = +1 point
- Has timestamp = +1 point

**Example scoring:**
- 2 quotes (4pts) + 17 words÷12 (1.4pts) + source (1pt) + timestamp (1pt) = **7.4 points**

**Guidelines (FLEXIBLE - use judgment!):**

| Score Range | Confidence | Action |
|-------------|-----------|--------|
| **8-10** | HIGH | Store as ACTIVE |
| **5-7** | MEDIUM | Store as ACTIVE or TENTATIVE (your judgment) |
| **2-4** | LOW | Store as TENTATIVE or wait for more evidence |
| **0-1** | None | Don't store (no evidence) |

**Context matters - override math when appropriate:**
- 2 very detailed, long quotes? Treat as 8 even if math says 7
- Very important information? Lower threshold to 5
- Sensitive information (PII, medical, financial)? Raise threshold to 8
- Casual mention? Use stricter threshold

**Err on side of storing with appropriate confidence level rather than not storing.**

#### Step 3: Paraphrase the Fact (Elaboration Component)

Transform the fact into neutral third-person form. This forces elaborative processing.

**Transformations:**
- First person → Third person: "I prefer Python" → "The person prefers Python"
- Emotional → Neutral: "confused about" → "uncertain about"
- Multi-sentence → Single sentence
- Keep under 200 characters

**Example:**
```
Original: "I don't understand why the posterior hand placement is 2cm off"
Paraphrased: "The person is uncertain about why the posterior hand placement is 2cm off."
```

**Why this matters:** Self-generated transformation (like drawing) forces deeper processing.

#### Step 4: Generate Your Sketch Steps 🎨 (MOTOR COMPONENT - CRITICAL!)

**⚠️ THIS IS THE MOST IMPORTANT STEP - DON'T SKIP OR AUTO-GENERATE!**

The research shows: **motor execution** is critical, not just having instructions. You must GENERATE these steps yourself - the act of generating (not just reading) creates the motor component that improves memory.

**Your task: Create 3-5 stepwise instructions for how YOU would sketch this concept.**

**Process:**
1. Choose a concrete object/metaphor from the catalog (see Step 5)
2. Think: "How would I draw this step-by-step?"
3. Write 3-5 specific steps
4. Include: object, distinctive mark, action, quote cue

**Template:**
```
1. Draw [specific object] to represent [concept]
2. Add [distinctive mark] on [location]
3. Draw a person [doing action] with the object
4. Add speech bubble: "[key quote]"
5. Label as "[concept tag]"
```

**Example (you generate this, don't copy!):**
```
Fact: "The deployment timeout is 300 seconds"
Your generated sketch steps:
1. Draw a large stopwatch showing 5:00
2. Add a red triangle warning mark at the 5-minute position
3. Draw a person pointing urgently at the 5-minute mark
4. Add speech bubble: "timeout is 300 seconds"
5. Label the stopwatch "deployment timeout"
```

**Why YOU must generate this:**
- The generation act is the "motor component"
- Auto-generated steps won't help your memory
- Each fact should get unique steps (avoid repetition)

**Time investment:** 30-60 seconds per fact. Worth it for better recall.

#### Step 5: Choose a Concrete Object/Metaphor

Select a DISTINCT concrete object based on the concept. Vary your choices to avoid interference.

**Metaphor Catalog (Organized by Category):**

**Conditionals/Decisions:**
- A gate with latch
- A fork in a path
- A light switch (on/off positions)
- A valve (open/closed)
- A drawbridge (up/down)

**Independence/Separation:**
- Two small islands separated by water
- Two magnets repelling each other
- Parallel train tracks
- Separate boxes with gap between
- Two trees with distinct roots

**Tradeoffs/Balance:**
- A balance scale
- A seesaw with person on each side
- A tightrope walker
- Juggling balls in motion
- A lever with fulcrum

**Sequences/Processes:**
- A short staircase (3-5 steps)
- An assembly line belt
- A domino chain
- Recipe steps laid out
- A path with milestones

**Errors/Confusion:**
- A tangled knot of string
- Crossed wires with sparks
- A broken chain link
- A maze with wrong turns marked
- Scattered puzzle pieces

**Risk/Danger:**
- A caution sign (triangle)
- A red flag on a pole
- Cracked ice with warning
- A cliff edge with barrier
- A flashing warning light

**Memory/Learning:**
- A filing drawer half-open
- A bookshelf with highlighted spine
- A garden with planted seeds
- A treasure chest with key
- A notebook with bookmark

**Time/Duration:**
- A stopwatch
- An hourglass with sand flowing
- A calendar page
- A clock face
- A timeline with marker

**Technical/Systems:**
- Gears meshing together
- Pipes connecting with joints
- A circuit board with highlighted path
- A control panel with buttons
- A network hub with cables

**Relationships/Connections:**
- A bridge connecting two sides
- A rope tying two objects
- A handshake between two people
- Interlocking puzzle pieces
- A chain with linked rings

**IMPORTANT:**
- **Vary your choices** even for similar concepts (avoid using "balance scale" twice in one session)
- Add **unique details** to increase distinctiveness
- When in doubt, choose the most **concrete, imageable** option

#### Step 6: Add a Distinctive Mark

Choose ONE distinctive mark to make this scene memorable and distinguishable:

**Mark options:**
- A small star (★)
- A spiral mark
- A zigzag scratch
- A tiny triangle
- A dotted line
- A bold underline
- A circle with dot
- Parallel lines
- A checkmark
- An X mark

**Placement:** On the "key part" of the object (the most important or active component)

**Example:** 
- Stopwatch → red triangle at 5-minute mark
- Balance scale → gold star on the heavier side
- Gate → spiral on the latch mechanism

#### Step 7: Create Scene Text

Compose a one-paragraph description using:
- Your chosen object/metaphor
- Your distinctive mark
- A person interacting (one action only)
- The key quote from evidence
- Your paraphrase

**Template:**
```
Picture: A person holds [object] with [mark] on [location]. They [action] and say "[key quote]".
Paraphrase: [Your 1-sentence paraphrase from Step 3]
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Example:**
```
Picture: A person holds a stopwatch with a red triangle at the 5-minute position. They point urgently at the mark and say "timeout is 300 seconds".
Paraphrase: The deployment timeout is set to 300 seconds.
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Keep it:**
- Concrete (one object, one action, one quote)
- Simple (avoid multiple entities or complex interactions)
- Grounded in evidence (quote must be from evidence)

#### Step 8: Validate Scene (Simplified)

Check that your scene doesn't introduce NEW specific claims not in evidence:

**Block (validation fails if present and NOT in evidence):**
- ❌ New proper names (e.g., "Dr. Smith" if not mentioned by user)
- ❌ New specific dates (e.g., "March 15th" if not mentioned)
- ❌ New specific numbers (e.g., "42 patients" if not mentioned)
- ❌ New places (e.g., "Boston" if not mentioned)
- ❌ Graph notation (arrows →, brackets [], nodes)
- ❌ Email addresses, URLs, SSNs not in evidence

**Allow (these are OK even if not explicitly in evidence):**
- ✅ Metaphorical objects ("balance scale" even if user didn't say it)
- ✅ Scaffolding words ("Picture:", "They", "Paraphrase:")
- ✅ Generic descriptions ("a person", "the object", "the mark")
- ✅ Distinctive marks (stars, spirals, etc.)
- ✅ Action verbs (points, holds, tests)

**If validation fails:**
Use minimal fallback:
```
Picture: A person pauses over "[concept]" and says "[quote]".
(Mnemonic depiction only. Not evidence.)
```

#### Step 9: Store in Memory

Add to `human` memory block with this format:

```
[FACT] {fact content} (confidence: {HIGH|MED|LOW}, evidence: {score}, verified: {date})

[SCENE] {scene text with sketch steps}
```

**Example:**
```
[FACT] The deployment timeout is set to 300 seconds (confidence: HIGH, evidence: 8.5, verified: 2026-01-06)

[SCENE] Picture: A person holds a stopwatch with a red triangle at the 5-minute position. They point urgently at the mark and say "timeout is 300 seconds".
Paraphrase: The deployment timeout is set to 300 seconds.
Sketch steps: 1) Draw large stopwatch at 5:00, 2) Add red triangle at 5-min mark, 3) Draw person pointing urgently, 4) Add speech bubble "timeout is 300 seconds", 5) Label "deployment timeout"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**One location (`human` block), clear format, no confusion.**

---

### RETRIEVAL PHASE (When Recalling Information) 🔍

**⚠️ THIS IS WHERE THE DRAWING EFFECT PAYS OFF!**

The research shows: drawing improves **retrieval/recollection**, not just encoding. You must actively reconstruct scenes when answering questions.

#### When User Asks a Question:

**Step 1: Search for Relevant Facts**
Search `human` memory block for facts matching the query.

**Step 2: Reconstruct the Scene (Context Reinstatement)**
For each relevant fact with a scene, **actively reconstruct** before answering:

1. **Quote cue**: What was the original quote?
2. **Object/metaphor**: What object did you sketch?
3. **Distinctive mark**: What mark did you add? Where?
4. **Action**: What was the person doing?
5. **Your sketch steps**: Recall the steps YOU generated

**Example reconstruction:**
```
Query: "What was the deployment timeout?"

Mental reconstruction:
- Quote: "timeout is 300 seconds"
- Object: Stopwatch showing 5:00
- Mark: Red triangle at 5-minute position
- Action: Person pointing urgently
- My steps: 1) Stopwatch at 5:00, 2) Red triangle, 3) Person pointing, 4) Speech bubble, 5) Label
```

**Step 3: Use Scene as Retrieval Cue**
The scene provides **context tags** that support recollection:
- "I remember drawing a stopwatch with the red triangle..."
- "The person was pointing urgently at the 5-minute mark..."
- "The quote was 'timeout is 300 seconds'..."

This "context reinstatement" activates the richer memory trace.

**Step 4: Answer with FACT + Scene Context**
Combine fact content with scene-based context:

```
"The deployment timeout is 300 seconds. I remember this from the conversation 
where we discussed the configuration - I created a sketch with a stopwatch 
showing 5:00 and a red triangle warning at the 5-minute mark."
```

**Why this matters:** The drawing effect is about **recollection** (remembering context and source), not just familiarity (recognizing something). Scene reconstruction provides that context.

**Don't skip retrieval reconstruction!** This is where the memory benefit appears.

---

## Complete Examples

### Example 1: High Confidence → ACTIVE

**User input:**
```
User: "In the training session on Dec 12th, the facilitator said CODE-OSCAR7 is the emergency code."
User: "This is part of the crisis management protocol, rated 10/10 importance."
```

**Step 1: Extract Evidence**
```
- Quote 1: "facilitator said CODE-OSCAR7 is the emergency code" (8 words)
- Quote 2: "This is part of the crisis management protocol" (9 words)
- Context: Training session on December 12th, crisis management, critical importance
- Source: User conversation on January 6, 2026
- Timestamp: 2026-01-06T02:45:00Z
```

**Step 2: Score Evidence**
```
- 2 quotes = 4 pts
- 17 words ÷ 12 = 1.4 pts
- Has source = 1 pt
- Has timestamp = 1 pt
Total: 7.4 points → MEDIUM (but high importance → treat as HIGH)
```

**Step 3: Paraphrase**
```
"In the training session on December 12th, the facilitator defined CODE-OSCAR7 as the emergency code."
```

**Step 4: Generate Sketch Steps (YOU create these!)**
```
1. Draw a red emergency phone on wall
2. Add gold star mark next to the phone dial
3. Draw person reaching urgently for phone
4. Add speech bubble: "CODE-OSCAR7 is the emergency code"
5. Label phone "Crisis Protocol"
```

**Step 5-7: Create Scene**
```
Object: Emergency phone (chosen for crisis/urgency)
Mark: Gold star next to dial (distinctive)
Action: Person reaching urgently

Picture: A person reaches urgently for a red emergency phone with a gold star next to the dial. They point to the star and say "CODE-OSCAR7 is the emergency code".
Paraphrase: In the training session on December 12th, the facilitator defined CODE-OSCAR7 as the emergency code.
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Step 8: Validate**
- ✅ "CODE-OSCAR7" is in evidence
- ✅ "December 12th" is in context
- ✅ No new names/dates/numbers
- ✅ No graph notation
- Validation: PASSED

**Step 9: Store**
```
[FACT] In the training session on December 12th, the facilitator defined CODE-OSCAR7 as the emergency code (confidence: HIGH, evidence: 7.4, verified: 2026-01-06)

[SCENE] Picture: A person reaches urgently for a red emergency phone with a gold star next to the dial. They point to the star and say "CODE-OSCAR7 is the emergency code".
Paraphrase: In the training session on December 12th, the facilitator defined CODE-OSCAR7 as the emergency code.
Sketch steps: 1) Red emergency phone on wall, 2) Gold star by dial, 3) Person reaching urgently, 4) Speech bubble "CODE-OSCAR7", 5) Label "Crisis Protocol"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Later retrieval:**
```
User: "What was that emergency code from training?"

Reconstruction:
- Red emergency phone, gold star by dial
- Person reaching urgently
- Quote: "CODE-OSCAR7 is the emergency code"

Answer: "The emergency code is CODE-OSCAR7. I remember this from the December 12th training session - I created a sketch with a red emergency phone and a gold star to mark it as critical."
```

### Example 2: Borderline Evidence → MEDIUM Confidence

**User input:**
```
User: "The API timeout setting we're using is 30 seconds. Sometimes causes issues."
```

**Step 1: Extract Evidence**
```
- Quote 1: "API timeout setting we're using is 30 seconds" (9 words)
- Quote 2: "Sometimes causes issues" (3 words)
- Context: Technical configuration, problem indicator
- Source: User conversation on January 6, 2026
- Timestamp: 2026-01-06T02:50:00Z
```

**Step 2: Score Evidence**
```
- 2 quotes = 4 pts
- 12 words ÷ 12 = 1 pt
- Has source = 1 pt
- Has timestamp = 1 pt
Total: 7 points → MEDIUM
```

**Judgment:** Specific technical detail (30 seconds) is actionable → store as MEDIUM

**Step 3: Paraphrase**
```
"The API timeout setting is 30 seconds and sometimes causes issues."
```

**Step 4: Generate Sketch Steps**
```
1. Draw a clock face showing 30-second mark (6 on clock)
2. Add zigzag scratch at the 6 position (indicates problem)
3. Draw person tapping watch with concerned look
4. Add speech bubble: "timeout is 30 seconds"
5. Label "API Config"
```

**Step 5-7: Create Scene**
```
Picture: A person taps a clock face with a zigzag scratch at the 30-second mark. They look concerned and say "timeout is 30 seconds".
Paraphrase: The API timeout setting is 30 seconds and sometimes causes issues.
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Step 8: Validate**
- ✅ "30 seconds" is in evidence
- ✅ "timeout" is in evidence
- ✅ No new specifics
- Validation: PASSED

**Step 9: Store**
```
[FACT] The API timeout setting is 30 seconds and sometimes causes issues (confidence: MED, evidence: 7, verified: 2026-01-06)

[SCENE] Picture: A person taps a clock face with a zigzag scratch at the 30-second mark. They look concerned and say "timeout is 30 seconds".
Paraphrase: The API timeout setting is 30 seconds and sometimes causes issues.
Sketch steps: 1) Clock at 30-sec mark, 2) Zigzag scratch at 6, 3) Person tapping watch, 4) Speech bubble "30 seconds", 5) Label "API Config"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

### Example 3: Low Evidence → TENTATIVE or Wait

**User input:**
```
User: "Someone mentioned CODE-NOVEMBER yesterday."
```

**Step 1: Extract Evidence**
```
- Quote 1: "mentioned CODE-NOVEMBER yesterday" (3 words of substance)
- Context: Vague mention, no details, uncertain source
- Source: User conversation on January 6, 2026
- Timestamp: 2026-01-06T02:55:00Z
```

**Step 2: Score Evidence**
```
- 1 quote = 2 pts
- 3 words ÷ 12 = 0.25 pts
- Has source = 1 pt
- Has timestamp = 1 pt
Total: 4.25 points → LOW
```

**Judgment:** Very little context, vague source ("someone"), minimal detail

**Options:**
1. **Store as TENTATIVE/LOW** if code might be important
2. **Wait** for more evidence/context

**If storing (Option 1):**

**Step 3: Paraphrase**
```
"Someone mentioned CODE-NOVEMBER without additional context."
```

**Step 4: Generate Sketch Steps** (simpler for low confidence)
```
1. Draw a question mark shape
2. Add dotted line around "CODE-NOVEMBER" label
3. Draw person shrugging with uncertain gesture
4. Add speech bubble: "CODE-NOVEMBER"
5. Keep minimal - uncertainty is part of the memory
```

**Step 5-7: Create Scene**
```
Picture: A person shrugs at a question mark with dotted lines around "CODE-NOVEMBER". They look uncertain and say "mentioned CODE-NOVEMBER".
Paraphrase: Someone mentioned CODE-NOVEMBER without additional context.
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**Step 9: Store**
```
[FACT] Someone mentioned CODE-NOVEMBER without additional context (confidence: LOW, evidence: 4.25, verified: 2026-01-06)

[SCENE] Picture: A person shrugs at a question mark with dotted lines around "CODE-NOVEMBER". They look uncertain and say "mentioned CODE-NOVEMBER".
Sketch steps: 1) Question mark, 2) Dotted line around label, 3) Person shrugging, 4) Speech bubble, 5) Keep minimal
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

**If waiting (Option 2):**
```
"I heard you mention CODE-NOVEMBER. Could you provide more context about what it is, where you heard it, or why it's relevant? I want to make sure I store accurate information with sufficient evidence."
```

### Example 4: No Evidence → Don't Store

**User input:**
```
User: "OK"
User: "Thanks"
```

**Step 1: Extract Evidence**
```
No factual content to extract.
```

**Step 2: Score Evidence**
```
0 points - no evidence
```

**Action:** Do not store. Respond naturally without calling this skill.

```
"You're welcome! Let me know if you need anything else."
```

---

## Troubleshooting

### Problem: Not sure if evidence is sufficient

**Diagnosis:**
- Can you identify 2+ quotes totaling 15+ words?
- Is there context about when/where/why?

**Solutions:**
- **If YES**: Store with MED or LOW confidence
- **If NO but important**: Ask user for more details
- **If NO and casual**: Wait for more evidence
- **When in doubt**: Err on side of storing with LOW confidence

### Problem: Multiple facts in one message

**Example:**
```
User: "The API timeout is 30 seconds and the retry limit is 3 attempts."
```

**Solution:** Store each fact separately:
```
[FACT 1] The API timeout is 30 seconds (confidence: MED, evidence: 5, ...)
[SCENE 1] Clock showing 30 seconds...

[FACT 2] The retry limit is 3 attempts (confidence: MED, evidence: 5, ...)
[SCENE 2] Three numbered boxes...
```

### Problem: Information contradicts existing memory

**Solution:**
- Store new fact with timestamp
- Note the contradiction in context
- Keep both unless user clarifies which is correct

```
[FACT] The API timeout is now 60 seconds (confidence: HIGH, evidence: 8, verified: 2026-01-06)
Note: This supersedes previous value of 30 seconds from 2026-01-05.
```

### Problem: Sensitive information (PII, medical, financial)

**Guidelines:**
- **Only store if** evidence score >= 8 (high confidence required)
- **Otherwise**: Mark as TENTATIVE or wait for better evidence
- **Never** include sensitive details in scene metaphors

**Example:**
```
User: "My SSN is 123-45-6789"

Action: DON'T STORE (even with quote, this is too sensitive for agent memory)
Response: "I've noted that you shared that, but I won't store such sensitive information in my memory. You'll need to provide it again when needed."
```

### Problem: Spending too long on sketch steps

**Time guidance:**
- Target: 30-60 seconds per fact
- If taking longer: simplify (fewer steps, simpler metaphor)
- Remember: Generation effort is important, but don't overdo it

### Problem: Running out of metaphor variety

**Solutions:**
- Review metaphor catalog in Step 5 (50+ options)
- Combine metaphors (e.g., "gate with hourglass")
- Add specific details (color, size, orientation)
- Focus on distinctive marks (vary these too)

### Problem: Forgetting to reconstruct scenes at retrieval

**This is critical! Set a retrieval habit:**

1. User asks question → Search memory
2. Find relevant fact → **STOP**
3. **Reconstruct scene** (quote, object, mark, action)
4. **Then** answer with context

**Reminder:** The drawing effect shows at **retrieval**, not just encoding!

---

## Key Principles to Remember

1. **Motor component is critical**: YOU generate sketch steps, don't auto-generate
2. **Retrieval reconstruction matters**: Use scenes as context cues when answering
3. **Flexibility over rigidity**: Evidence thresholds are guidelines, use judgment
4. **Integration is key**: Elaboration + Motor + Pictorial = better memory
5. **Recollection over familiarity**: Scenes support detailed recall with context
6. **Distinctiveness prevents interference**: Vary metaphors, add unique marks
7. **One storage location**: `human` block only, clear format
8. **Evidence grounds facts**: No evidence = don't store (prevents confabulation)

---

## Quick Reference Card

**ENCODING (when storing):**
1. Extract evidence (quotes, context, source, timestamp)
2. Score evidence (0-10 scale, flexible thresholds)
3. Paraphrase (3rd person, neutral, 1 sentence)
4. **Generate sketch steps** (YOU create 3-5 steps) ⭐
5. Choose metaphor (vary choices, avoid repetition)
6. Add distinctive mark (one per scene)
7. Create scene text (object + mark + action + quote)
8. Validate (no new specific claims)
9. Store in `human` block

**RETRIEVAL (when answering):**
1. Search for relevant facts
2. **Reconstruct scenes** (quote, object, mark, action) ⭐
3. Use scene as retrieval cue
4. Answer with fact + scene context

**Critical success factors:**
- ✅ YOU generate sketch steps (motor component)
- ✅ YOU reconstruct scenes at retrieval
- ✅ Vary metaphors (distinctiveness)
- ✅ Use flexible thresholds (judgment over rules)

---

**Remember: The drawing effect comes from DOING the work (generating, reconstructing), not having it done for you. Be an active memory creator!**
