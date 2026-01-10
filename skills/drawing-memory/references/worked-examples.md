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

