# Edge Cases and Special Situations

## Case 1: Technical Documentation with Specific Values

**User input:**
```
User: "According to the README, the rate limit is 100 requests per minute with a burst allowance of 150. This was updated last week."
```

**Analysis:**
```
Evidence:
- Quote 1: "rate limit is 100 requests per minute with a burst allowance of 150" (13 words)
- Quote 2: "This was updated last week" (5 words)
- Context: From README documentation, recent update
- Source: User conversation, referencing documentation
- Timestamp: 2026-01-06T15:00:00Z

Score:
- 2 quotes = 4 pts
- 18 words ÷ 12 = 1.5 pts
- Source + timestamp = 2 pts
Total: 7.5 points -> MEDIUM, but specific technical values -> treat as HIGH
```

**Sketch steps you generate:**
```
1. Draw a speedometer with needle at "100" mark
2. Add a second smaller dial showing "150" (burst)
3. Add spiral mark on the burst dial
4. Draw person pointing to both dials
5. Label "Rate Limit - Updated"
Speech bubble: "100 per minute, burst 150"
```

**Storage:**
```
[FACT] The rate limit is 100 requests per minute with a burst allowance of 150 (confidence: HIGH, evidence: 7.5, verified: 2026-01-06)

[SCENE] Picture: A person points to a speedometer at "100" and a smaller dial at "150" with a spiral mark. They say "100 per minute, burst 150".
Paraphrase: The rate limit is 100 requests per minute with a burst allowance of 150, updated recently.
Sketch steps: 1) Speedometer at 100, 2) Smaller burst dial at 150, 3) Spiral on burst dial, 4) Person pointing to both, 5) Label "Rate Limit - Updated"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

---

## Case 2: Multiple Related Facts in Context

**User input:**
```
User: "For the production deployment: use port 443, enable SSL, and set the connection pool to 50. Dev environment uses port 8080 without SSL."
```

**Analysis:**
Two separate facts (prod vs dev), store separately:

**FACT 1 - Production:**
```
Evidence:
- Quote: "production deployment: use port 443, enable SSL, and set the connection pool to 50" (14 words)
- Context: Production environment configuration
Score: 2 + 1.17 + 2 = 5.17 -> MEDIUM, but important -> HIGH

Sketch steps:
1. Draw a locked padlock (SSL/security)
2. Add "443" label on the lock body
3. Add "pool:50" on a tag attached to lock
4. Add gold star mark on keyhole
5. Draw person holding key to lock
```

**FACT 2 - Development:**
```
Evidence:
- Quote: "Dev environment uses port 8080 without SSL" (7 words)
- Context: Development environment configuration
Score: 2 + 0.58 + 2 = 4.58 -> LOW/MEDIUM

Sketch steps:
1. Draw an open padlock (no SSL)
2. Add "8080" label on open lock
3. Add dotted line mark (indicates "dev/testing")
4. Draw person pointing at open lock
5. Label "Dev Config"
```

**Storage:**
```
[FACT] Production deployment uses port 443, SSL enabled, connection pool of 50 (confidence: HIGH, evidence: 5.17, verified: 2026-01-06)
[SCENE] Locked padlock with "443" and "pool:50", gold star on keyhole...

[FACT] Development environment uses port 8080 without SSL (confidence: MED, evidence: 4.58, verified: 2026-01-06)
[SCENE] Open padlock with "8080", dotted line mark...
```

---

## Case 3: Correction of Previous Information

**User input:**
```
User: "Actually, I was wrong earlier. The timeout is 60 seconds, not 30. Sorry for the confusion."
```

**Analysis:**
```
Evidence:
- Quote 1: "the timeout is 60 seconds, not 30" (7 words)
- Quote 2: "Sorry for the confusion" (4 words - confirms intentional correction)
- Context: Explicit correction of previous fact
Score: 4 + 0.92 + 2 = 6.92 -> MEDIUM, but correction context -> HIGH

Action: Store new fact, note supersedes previous
```

**Sketch steps:**
```
1. Draw a clock showing 60 seconds (1 minute mark)
2. Add small crossed-out "30" below the 60
3. Add checkmark mark on the 60
4. Draw person nodding with correcting gesture
5. Label "Timeout - CORRECTED"
```

**Storage:**
```
[FACT] The timeout is 60 seconds (confidence: HIGH, evidence: 6.92, verified: 2026-01-06)
NOTE: This supersedes previous value of 30 seconds from earlier today.

[SCENE] Picture: A person nods and points to "60" on a clock with a crossed-out "30" below and a checkmark on the 60. They say "timeout is 60 seconds, not 30".
Paraphrase: The timeout is 60 seconds, correcting the previous value of 30 seconds.
Sketch steps: 1) Clock at 60 seconds, 2) Crossed-out "30" below, 3) Checkmark on 60, 4) Person nodding, 5) Label "CORRECTED"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

---

## Case 4: Sensitive Information (DON'T Store)

**User input:**
```
User: "My password for the admin panel is MyP@ssw0rd123"
```

**Analysis:**
```
Evidence score: Would be ~7 (good quote, source, timestamp)
BUT: Sensitive information (password/credential)

Decision: DON'T STORE
```

**Response:**
```
"I've noted that you shared your admin password, but I won't store credentials in my memory for security reasons. You'll need to provide it again when needed, or better yet, consider using a password manager."
```

**Alternative for non-credential sensitive info:**
```
If it's medical/financial but user explicitly wants storage:
- Require evidence score >= 9 (very high bar)
- Use GENERIC_MAX scene (minimal, no specific details)
- Mark as TENTATIVE even with high score
```

---

## Case 5: Uncertain or Speculative Information

**User input:**
```
User: "I think the meeting is on Thursday, but it might be Friday. Not totally sure."
```

**Analysis:**
```
Evidence:
- Quote: "meeting is on Thursday, but it might be Friday. Not totally sure" (12 words)
- Context: Uncertainty indicators throughout
- Source + timestamp: 2 pts
Score: 2 + 1 + 2 = 5 -> MEDIUM mathematically

BUT uncertainty is explicit -> Lower to LOW or DON'T store

Better action: ASK FOR CLARIFICATION
```

**Response:**
```
"I heard you mention a meeting on Thursday or Friday, but you're not certain. Would you like me to help you check your calendar, or shall we wait until you confirm the date? I prefer to store accurate information."
```

**If user says "just store it as uncertain":**
```
Sketch steps:
1. Draw a calendar with Thursday AND Friday highlighted
2. Add question mark mark between the two days
3. Draw person shrugging with uncertain gesture
4. Speech bubble: "Thursday, but might be Friday"
5. Label "Meeting - UNCONFIRMED"

Store with LOW confidence and note uncertainty explicitly.
```

---

## Case 6: Complex Multi-Step Process

**User input:**
```
User: "To deploy: first run the tests, then build the Docker image, push to registry, and finally update the k8s deployment. Always do them in that order."
```

**Analysis:**
```
Evidence:
- Quote 1: "run the tests, then build the Docker image, push to registry, and finally update the k8s deployment" (17 words)
- Quote 2: "Always do them in that order" (6 words)
- Context: Deployment procedure, sequence is critical
Score: 4 + 1.92 + 2 = 7.92 -> HIGH (close to 8, sequential process)
```

**Sketch steps (sequence metaphor):**
```
1. Draw a staircase with 4 steps labeled: "Test", "Build", "Push", "Deploy"
2. Add arrow marks pointing up the stairs (sequence direction)
3. Draw person at bottom of stairs pointing upward
4. Speech bubble: "always do them in that order"
5. Add warning triangle at bottom (emphasizes "always")
```

**Storage:**
```
[FACT] Deployment sequence: run tests, build Docker image, push to registry, update k8s deployment - must be done in that order (confidence: HIGH, evidence: 7.92, verified: 2026-01-06)

[SCENE] Picture: A person stands at the bottom of a 4-step staircase labeled "Test->Build->Push->Deploy" with arrow marks pointing upward and a warning triangle. They point up and say "always do them in that order".
Paraphrase: The deployment sequence is tests, build, push, deploy, and must be followed in that order.
Sketch steps: 1) 4-step staircase with labels, 2) Arrow marks up stairs, 3) Person at bottom pointing up, 4) Speech bubble, 5) Warning triangle at base
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

---

## Case 7: Previously Unknown Technical Term

**User input:**
```
User: "The CQRS pattern means Command Query Responsibility Segregation. We're using it for the order service."
```

**Analysis:**
```
Evidence:
- Quote 1: "CQRS pattern means Command Query Responsibility Segregation" (7 words)
- Quote 2: "We're using it for the order service" (7 words)
- Context: Definition + application context
Score: 4 + 1.17 + 2 = 7.17 -> MEDIUM, but definitional -> HIGH
```

**Sketch steps (concept + application):**
```
1. Draw two separate boxes labeled "Command" and "Query"
2. Add a line separating them (represents segregation)
3. Add small "CQRS" label bridging both boxes
4. Draw person pointing to the separation line with emphasis
5. Add "Order Service" tag below
Speech bubble: "Command Query Responsibility Segregation"
```

**Storage:**
```
[FACT] CQRS pattern means Command Query Responsibility Segregation, used in the order service (confidence: HIGH, evidence: 7.17, verified: 2026-01-06)

[SCENE] Picture: A person points to two separate boxes labeled "Command" and "Query" with a line between them and "CQRS" bridging both. "Order Service" tag below. They say "Command Query Responsibility Segregation".
Paraphrase: CQRS (Command Query Responsibility Segregation) pattern is used in the order service.
Sketch steps: 1) Two boxes "Command"/"Query", 2) Separation line, 3) "CQRS" label, 4) Person pointing at line, 5) "Order Service" tag
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

---

## Case 8: Time-Sensitive Information

**User input:**
```
User: "The sale ends tonight at midnight. Code is SAVE20 for 20% off."
```

**Analysis:**
```
Evidence:
- Quote 1: "sale ends tonight at midnight" (5 words)
- Quote 2: "Code is SAVE20 for 20% off" (6 words)
- Context: Time-sensitive, promotional
Score: 4 + 0.92 + 2 = 6.92 -> MEDIUM

Time-sensitive consideration:
- Store with expiration note
- Or ask: "Should I remind you of this timing?"
```

**Sketch steps:**
```
1. Draw a clock showing midnight (12:00)
2. Add "SAVE20" label next to clock with "20%" tag
3. Add urgent mark (red triangle or exclamation)
4. Draw person pointing urgently at clock
5. Label "EXPIRES TONIGHT"
```

**Storage:**
```
[FACT] Sale ends tonight at midnight, code SAVE20 for 20% off (confidence: MED, evidence: 6.92, verified: 2026-01-06, EXPIRES: 2026-01-06T23:59:59Z)

[SCENE] Picture: A person points urgently at a clock showing midnight with "SAVE20 - 20%" next to it and a red triangle mark. They say "ends tonight at midnight, SAVE20".
Paraphrase: The sale ends tonight at midnight with code SAVE20 for 20% off.
Sketch steps: 1) Clock at midnight, 2) "SAVE20 - 20%" label, 3) Red triangle urgent mark, 4) Person pointing urgently, 5) "EXPIRES TONIGHT"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)

NOTE: This information expires 2026-01-06 at midnight.
```

---

## Case 9: Conflicting Information from Multiple Sources

**User input:**
```
User: "According to the docs, the limit is 100. But Sarah from DevOps said they increased it to 200 last week."
```

**Analysis:**
```
Two potentially conflicting facts:
1. Documentation says 100 (older source?)
2. Sarah says 200 (recent change?)

Best practice: Store both with context, or ask for clarification
```

**Approach 1: Ask for clarification**
```
"I see conflicting information - the documentation says 100, but Sarah mentioned it's now 200. Do you know which is current, or would you like me to store both with their sources?"
```

**Approach 2: Store both if user wants**
```
[FACT] The limit is 100 according to documentation (confidence: MED, evidence: 5, verified: 2026-01-06, SOURCE: docs)

[FACT] The limit is 200 according to Sarah from DevOps (confidence: MED, evidence: 5, verified: 2026-01-06, SOURCE: Sarah, recent change)

NOTE: Conflicting values - may need verification.
```

---

## Case 10: User Explicitly Requests Storage

**User input:**
```
User: "Remember that I prefer tabs over spaces for Python code."
```

**Analysis:**
```
Evidence:
- Quote: "I prefer tabs over spaces for Python code" (8 words)
- Context: Explicit request ("Remember that...")
- Source + timestamp: 2 pts
Score: 2 + 0.67 + 2 = 4.67 -> LOW mathematically

BUT: Explicit request + preference (low risk) -> store as MEDIUM
```

**Sketch steps:**
```
1. Draw two parallel horizontal lines (represents tabs)
2. Draw several dots next to it (represents spaces)
3. Add checkmark on tabs, X on spaces
4. Draw person pointing at tabs with approval gesture
5. Label "Python Code Style"
```

**Storage:**
```
[FACT] The person prefers tabs over spaces for Python code (confidence: MED, evidence: 4.67, verified: 2026-01-06)

[SCENE] Picture: A person points approvingly at parallel lines (tabs) with a checkmark, next to dots (spaces) with an X. They say "prefer tabs over spaces for Python".
Paraphrase: The person prefers using tabs over spaces for Python code.
Sketch steps: 1) Parallel lines (tabs), 2) Dots (spaces), 3) Checkmark on tabs / X on spaces, 4) Person approving tabs, 5) "Python Style"
(Mnemonic depiction only; metaphor may be imperfect. Not evidence.)
```

---

## General Guidelines from These Cases

**When to be more liberal (store despite lower score):**
- Explicit user request ("remember that...")
- Preferences (low risk if wrong)
- Technical definitions (verifiable)
- User corrects themselves (shows intentionality)
- Specific values even with short quotes

**When to be more conservative (require higher score):**
- Sensitive information (PII, credentials, medical)
- Uncertainty indicators ("I think", "maybe", "might be")
- Conflicting information
- Time-sensitive with short validity
- Irreversible actions

**When to ask for clarification:**
- Conflicting sources
- Uncertainty in evidence
- High importance + low evidence
- Seems incomplete

**When to absolutely NOT store:**
- Credentials (passwords, API keys, tokens)
- SSNs, credit cards, other protected data
- Explicit user request not to remember
- No evidence at all (pure speculation)
