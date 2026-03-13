# Dual-Trace Memory Skill for Letta Agents

Research-backed memory encoding for `letta_v1_agent` -- validated at +19.7
percentage points overall accuracy on LongMemEval-S versus fact-only encoding.

- This creation and organization of this repository was accomplished with
  assistance from a Letta Code agent.

---

## What It Does

When a user shares personal information, the agent stores two linked traces in
archival memory rather than one:

  [FACT:anchor] -- a structured third-person paraphrase preserving all
  components of the information. Optimized for compositional accuracy.

  [SCENE:anchor] -- a distinctive visual scene with temporal anchors embedding
  the information as a visible label or quote. Optimized for recollection,
  temporal sequencing, and multi-session aggregation.

At retrieval time, the agent uses archival_memory_search to find both traces
and synthesizes a confident, context-rich answer.

The dual-trace approach is inspired by the drawing effect (Fernandes et al.,
2018): encoding the same information in two representational formats produces
stronger, more distinctive memory traces than encoding it once.

---

## Empirical Validation

Evaluated on LongMemEval-S: 4,575 real user conversation sessions used as a
teach corpus, 100 structured recall questions, 4,575-session distractor
haystack, GPT-4o graded against ground-truth oracle (March 2026).

Controlled comparison: C6 (dual-trace) vs C7 (fact-only). Both conditions
used identical evidence rates (~63-65%), identical archival passage format,
and the same letta_v1_agent architecture. The only variable was the presence
or absence of scene traces.

| Question type      | C7 fact-only | C6 dual-trace | Scene contribution |
|--------------------|--------------|---------------|--------------------|
| Overall            | 54%          | 73.7%         | +19.7 pp           |
| Single-session     | 79%          | 79%           | 0 pp (null)        |
| Multi-session      | 43%          | 65.5%         | +22.2 pp           |
| Knowledge-update   | 59%          | 81.8%         | +22.7 pp           |
| Temporal-reasoning | 38%          | 70.8%         | +33.3 pp           |

The single-session null result is expected and meaningful: scenes do not help
when a single lookup suffices. Scenes contribute specifically when memory must
be aggregated across sessions, sequenced in time, or resolved when conflicting
entries exist. This matches episodic encoding theory.

Full performance ladder (LME-S benchmark):

| Condition               | Overall | Description                         |
|-------------------------|---------|-------------------------------------|
| Vanilla (no memory)     | ~9%     | No stored sessions                  |
| Basic archival (C4)     | 48%     | Selective storage, older format     |
| Fact-only (C7)          | 54%     | High coverage + clean anchor format |
| Dual-trace (C6)         | 73.7%   | C7 + scene traces                   |
| SOTA (published)        | 84-86%  | BM25 + dense retrieval + reranking  |

See skills/drawing-memory/references/worked-examples.md for three annotated
real-world examples showing each retrieval mechanism in action.

---

## Quick Start

### Prerequisites

- A Letta account at app.letta.com (or a self-hosted Letta server)
- Python 3.9+ with the Letta Python SDK: `pip install letta-client`
- Git

> **Recommended model:** All LME-S evaluation runs (C6 and C7) used
> `anthropic/claude-sonnet-4-6`. The 73.7% accuracy result is specific to
> this model. We recommend using claude-sonnet-4-6 to replicate the results.

### Step 1 -- Clone this repository

```bash
git clone https://github.com/sternb12/agent_draw_skills.git
cd agent_draw_skills
```

### Step 2 -- Create a letta_v1_agent

Create an agent via the Python SDK. Copy the persona from the skill file:

```python
from letta_client import Letta

client = Letta(api_key="your-api-key")

# Read the skill content
with open("skills/drawing-memory/SKILL.md") as f:
    skill_content = f.read()

agent = client.agents.create(
    name="my-dual-trace-agent",
    agent_type="letta_v1_agent",
    model="anthropic/claude-sonnet-4-6",
    memory_blocks=[
        {
            "label": "system/persona",
            "value": skill_content
        }
    ],
    tools=[
        "archival_memory_insert",
        "archival_memory_search",
        "memory_replace",
        "conversation_search"
    ]
)

print(f"Agent created: {agent.id}")
```

### Step 3 -- Send a session (encode)

```python
response = client.agents.messages.create(
    agent_id=agent.id,
    messages=[{
        "role": "user",
        "content": "Hi, I'm Sarah. I'm a physical therapist in Boston "
                   "specializing in ACL rehab. I just ran my fourth marathon."
    }]
)
print(response.messages[-1].content)
# Agent scores evidence, routes to FULL, and calls archival_memory_insert
# with a [FACT:work-occupation] + [SCENE:work-occupation] passage.
```

### Step 4 -- Ask a recall question

```python
response = client.agents.messages.create(
    agent_id=agent.id,
    messages=[{
        "role": "user",
        "content": "What do you know about my work?"
    }]
)
print(response.messages[-1].content)
# Agent calls archival_memory_search("work-occupation"), finds FACT + SCENE,
# and returns a confident answer with context.
```

### Step 5 -- Verify what was stored

```python
passages = list(client.agents.passages.list(agent_id=agent.id))
facts  = sum(1 for p in passages if "[FACT:"  in (p.text or ""))
scenes = sum(1 for p in passages if "[SCENE:" in (p.text or ""))
print(f"{len(passages)} passages ({facts} FACT, {scenes} SCENE)")
```

---

## Architecture

**Agent type:** `letta_v1_agent`

**Tools required:**
- `archival_memory_insert` -- stores [FACT:anchor] + [SCENE:anchor] passages
- `archival_memory_search` -- retrieves passages by anchor label
- `memory_replace` -- updates persona block if needed
- `conversation_search` -- optional, for searching recent message history

**Memory structure:** All encoded information lives in archival memory as
passages containing [FACT:anchor] and [SCENE:anchor] text tags. No additional
memory blocks or file system access required. The skill instructions live in
the system/persona block.

**Evidence gate:** The agent scores each incoming session on three dimensions
(Relevance, Specificity, Explicitness, 0-6 total). Sessions scoring 0-2 are
dropped. Sessions scoring 3-6 trigger dual-trace encoding.

**Passage format:**
```
[FACT:anchor]
{structured third-person paraphrase}

[SCENE:anchor]
Picture: {concrete object + distinctive mark + embedded key text}
Sketch steps: (1) Draw {object}, (2) Add {mark}, (3) Embed "{quote}"
(Mnemonic depiction only. Not evidence.)
```

---

## Repository Structure

```
agent_draw_skills/
+-- README.md                          <- this file
+-- skills/
    +-- drawing-memory/
        +-- SKILL.md                   <- encoding + retrieval instructions
        +-- references/
            +-- worked-examples.md     <- 3 annotated LME-S examples
            +-- evidence-scoring-details.md
            +-- token-analysis.md      <- C6 vs C7 cost comparison
```

---

## Research Citation

Fernandes, M. A., Wammes, J. D., & Meade, M. E. (2018). The surprisingly
powerful influence of drawing on memory. Current Directions in Psychological
Science, 27(5), 302-308. https://doi.org/10.1177/0963721418755385

---

## Notes

This repository implements the archival memory (letta_v1_agent) version of
dual-trace encoding, which corresponds to the C6 condition evaluated on
LME-S (73.7% overall accuracy).

A separate file-based implementation for Letta Code agents (which uses
Write/Read/Glob tools and the agent memory filesystem rather than archival
memory) is available at: https://github.com/sternb12/letta-code-draw-skill

---

**Version:** 3.0.0
**Last validated:** March 2026 (LME-S controlled evaluation)
**License:** MIT
