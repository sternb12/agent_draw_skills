# Edge Cases and Special Situations

All examples use the three-dimension evidence scoring (Relevance + Specificity +
Explicitness, 0-6 total) and the DROP/FULL routing defined in SKILL.md. Storage
format is archival_memory_insert with [FACT:anchor] + [SCENE:anchor] passages.

---

## Case 1: User Asks About a Topic Without Disclosing Personal Information

**User input:**
  "What are some good strategies for managing work stress?"

**Scoring:**
  Relevance:    0 -- user is asking a general question, no self-disclosure
  Specificity:  0 -- no personal context stated
  Explicitness: 0 -- nothing stated, everything is implied at best
  Total: 0 -> DROP

**Action:** Answer the question naturally. Do not call archival_memory_insert.

**Why this matters:** Relevance = 0 is the most common source of over-encoding.
The agent must distinguish "user is curious about X" from "user is experiencing X."
Asking about stress management does not mean the user is stressed.

---

## Case 2: Incidental Self-Disclosure While Asking a Question

**User input:**
  "I run about three times a week -- any tips for avoiding shin splints?"

**Scoring:**
  Relevance:    1 -- user incidentally reveals personal context while asking
  Specificity:  1 -- general frequency, no specific distance, event, or goal
  Explicitness: 1 -- casual mention in passing, not a direct statement
  Total: 3 -> FULL (just clears the threshold)

**Stored via archival_memory_insert:**

  [FACT:hobby-running]
  The user runs approximately three times per week.

  [SCENE:hobby-running]
  Picture: A pair of running shoes on a mat by the front door, three days
  circled on a weekly planner on the wall beside it. A handwritten note
  reads "shin splints -- ask about this."
  Sketch steps: (1) Draw running shoes by a door with a weekly planner, (2)
  Circle three days on the planner, (3) Embed "3x/week" as a label pinned
  next to the circles.
  (Mnemonic depiction only. Not evidence.)

**Note:** This is a borderline case. At score 3 the information is sparse but
real. If the user later mentions a specific race, distance, or goal, that passage
would be a natural upgrade point (re-score, update FACT, add richer SCENE).

---

## Case 3: Direct, High-Evidence Disclosure -- Straightforward FULL

**User input:**
  "I'm a 42-year-old nurse practitioner at a pediatric clinic in Denver.
  I've been in pediatrics for about 15 years."

**Scoring:**
  Relevance:    2 -- explicit personal fact directly stated
  Specificity:  2 -- includes role, setting, city, and years of experience
  Explicitness: 2 -- direct statement with no ambiguity
  Total: 6 -> FULL

**Stored via archival_memory_insert:**

  [FACT:personal-identity]
  The user is 42 years old and works as a nurse practitioner at a pediatric
  clinic in Denver, Colorado. They have approximately 15 years of experience
  in pediatrics.

  [SCENE:personal-identity]
  Picture: A clinic hallway -- pediatric posters on the wall, a stethoscope
  around the neck of someone in scrubs. A small nameplate on an exam room
  door. A calendar on the wall shows years of continuity: the same clinic,
  the same specialty, for a decade and a half. Denver skyline visible through
  a window at the end of the hall.
  Sketch steps: (1) Draw a pediatric clinic hallway with posters and exam
  room doors, (2) Add a stethoscope and scrubs on the figure in the hall,
  (3) Embed "15 years" on a wall plaque and "Denver" on the window view.
  (Mnemonic depiction only. Not evidence.)

---

## Case 4: Explicit User Correction of Previously Stored Information

**User input:**
  "Actually, I should correct myself -- I moved to Austin last year, not
  Dallas. I got confused. I've been in Austin since March 2024."

**Scoring:**
  Relevance:    2 -- explicit personal fact, directly stated
  Specificity:  2 -- specific city and move date provided
  Explicitness: 2 -- user explicitly corrects themselves
  Total: 6 -> FULL

**Action:** Before encoding, search archival memory for the previous city
reference. If found, note the correction in the new passage; do not overwrite
silently. Update the FACT trace with the correct information. The SCENE should
reflect the correction with a temporal anchor for the move.

**Stored via archival_memory_insert:**

  [FACT:location-city]
  The user lives in Austin, Texas. They relocated to Austin in March 2024.
  (A previous entry incorrectly recorded Dallas -- this supersedes it.)

  [SCENE:location-city]
  Picture: Moving boxes stacked in a new apartment, a calendar on the wall
  showing March 2024 with a circle around the move-in date. An Austin skyline
  visible through the window. A sticky note on one box reads "NOT Dallas."
  Sketch steps: (1) Draw moving boxes in a bare apartment with March 2024
  calendar, (2) Add Austin skyline through the window, (3) Embed "Austin,
  March 2024" on the calendar circle.
  (Mnemonic depiction only. Not evidence.)

---

## Case 5: Uncertain Information -- Ask Before Encoding

**User input:**
  "I think my follow-up appointment is sometime next week, maybe Tuesday
  or Wednesday. I'm not totally sure."

**Scoring:**
  Relevance:    2 -- personal event directly mentioned
  Specificity:  1 -- approximate range, no confirmed date
  Explicitness: 1 -- heavy uncertainty markers throughout
  Total: 4 -> FULL by score, but uncertainty overrides

**Action:** Do not encode uncertain information as if it were confirmed. Ask
for clarification before storing:

  "I heard you mention a follow-up appointment next week, possibly Tuesday
  or Wednesday -- but you said you're not totally sure. Would you like me
  to hold off until you confirm the date, or should I store it as uncertain
  for now?"

If the user says store it as uncertain, encode with LOW confidence noted in
the FACT trace:

  [FACT:appointment-followup]
  The user has a follow-up appointment tentatively scheduled for next week,
  either Tuesday or Wednesday (unconfirmed as of this conversation).

  [SCENE:appointment-followup]
  Picture: A calendar open to next week with Tuesday and Wednesday both
  lightly circled in pencil -- neither circled firmly. A question mark
  written between the two days. A phone on the desk suggesting the date
  still needs to be confirmed.
  Sketch steps: (1) Draw a weekly calendar with pencil circles on Tuesday
  and Wednesday, (2) Add a question mark between the two days, (3) Embed
  "unconfirmed" as a small note below the circles.
  (Mnemonic depiction only. Not evidence.)

---

## Case 6: Information That Must Not Be Stored -- Credentials

**User input:**
  "My login for the patient portal is nurse42 and the password is BlueSky!99."

**Scoring:**
  Relevance:    2 -- explicit personal fact
  Specificity:  2 -- exact credentials provided
  Explicitness: 2 -- direct statement
  Total: 6 -- would score FULL

**Action:** Do not store credentials regardless of score. Passwords, API keys,
SSNs, and security tokens must not be encoded into archival memory.

Respond:
  "I can note that you use a patient portal, but I won't store login
  credentials or passwords -- that is not safe to keep in my memory.
  Please use a password manager for that. Is there anything else about
  your work tools I can help with?"

You may store the fact that the user uses a patient portal without storing
the credentials themselves:

  [FACT:work-tools-patient-portal]
  The user uses a patient portal as part of their clinical workflow.

---

## Case 7: Same Anchor, Second Session -- Knowledge Update

The user previously mentioned they exercise three times a week (stored under
hobby-running). In a new session:

**User input:**
  "I've been training for my first half marathon -- up to five runs a week
  now, mostly early mornings before work."

**Scoring:**
  Relevance:    2 -- explicit update to personal context
  Specificity:  2 -- specific frequency, goal, and time of day
  Explicitness: 2 -- direct statement
  Total: 6 -> FULL

**Action:** Do not search for contradictions and discard. Instead, encode a
new passage with the current information. Both the old and new entries will
exist in archival memory; at retrieval time, the SCENE temporal anchors will
help determine which is current.

**Stored via archival_memory_insert (new passage -- does not replace old):**

  [FACT:hobby-running]
  The user is training for their first half marathon. They now run five times
  per week, primarily early mornings before work. (Updated from approximately
  three times per week noted in an earlier session.)

  [SCENE:hobby-running]
  Picture: An early morning, pre-dawn. Running shoes by the door, an alarm
  clock showing 5:30 AM. A half marathon training plan is pinned to a
  corkboard on the wall -- weeks blocked off in a grid, the target race date
  circled. Five weekdays are highlighted on the weekly view.
  Sketch steps: (1) Draw a dark pre-dawn doorway with running shoes, (2) Add
  a 5:30 AM alarm clock and training plan corkboard, (3) Embed "5x/week" on
  the training plan grid and "first half marathon" on the race date circle.
  (Mnemonic depiction only. Not evidence.)

**Retrieval note:** When asked "how often do you exercise?" retrieve both
passages. The most recent SCENE (training plan, 5:30 AM alarm) signals the
current state. If dates are not explicit, the temporal anchor context (pre-dawn
training intensity vs casual three-times-a-week) helps identify which is current.

---

## Case 8: Multiple Distinct Facts in a Single Message

**User input:**
  "I have two kids -- my daughter is 8 and my son just turned 5 last month.
  My partner is a high school teacher in the same district where I work."

**Action:** Identify distinct topics and store separately. Combining unrelated
facts into one passage creates retrieval problems -- a search for "partner" would
have to surface a passage primarily about "children," and vice versa.

Store two separate archival_memory_insert calls:

**Insert 1:**

  [FACT:family-children]
  The user has two children: a daughter aged 8 and a son who recently turned 5.

  [SCENE:family-children]
  Picture: A living room with a child's artwork on the refrigerator. A 5-year-
  old's birthday balloons are still up -- recent, just last month. An 8-year-old's
  backpack hangs by the door next to a smaller one. Two distinct sizes.
  Sketch steps: (1) Draw a living room with refrigerator artwork and a backpack
  area showing two different sizes, (2) Add birthday balloons to signal the 5-
  year-old's recent birthday, (3) Embed "age 8" and "just turned 5" as labels
  near each backpack.
  (Mnemonic depiction only. Not evidence.)

**Insert 2:**

  [FACT:family-partner]
  The user's partner is a high school teacher in the same school district where
  the user works.

  [SCENE:family-partner]
  Picture: Two people walking into a large district office building at the same
  time, both carrying teacher bags. A sign on the building reads the district
  name. They work in the same system, different buildings, same commute direction.
  Sketch steps: (1) Draw two adults entering a district office building together,
  (2) Add teacher bags for both, (3) Embed "same district" on the building sign.
  (Mnemonic depiction only. Not evidence.)

---

## Summary: Key Routing Decisions

| Situation | Score | Action |
|---|---|---|
| General question, no self-disclosure | 0 | DROP |
| Incidental mention, borderline | 3 | FULL (but sparse -- revisit later) |
| Clear explicit fact, specific | 5-6 | FULL |
| Uncertainty markers throughout | Any | Ask first, encode as uncertain if user wants |
| Credentials (password, token) | Any | Never store -- explain why |
| Knowledge update to existing anchor | 5-6 | FULL (new passage, keep old for temporal context) |
| Multiple distinct topics in one message | Any | Separate inserts per topic |
