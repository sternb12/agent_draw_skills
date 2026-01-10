# How Skills Work in Letta - Complete Architecture

## Overview Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         LETTA SKILLS ARCHITECTURE                            │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ PHASE 1: DISCOVERY & INITIALIZATION (Startup)                               │
└─────────────────────────────────────────────────────────────────────────────┘

    Filesystem                      Letta Client                Agent Memory
    ──────────                      ────────────                ────────────

    .skills/                        
    ├── drawing-memory/             
    │   ├── SKILL.md         ──────> Scan & Parse  ──────>  ┌──────────────┐
    │   ├── README.md                Frontmatter            │skills block  │
    │   ├── references/                                     │(read-only)   │
    │   └── examples/                Extract:               │              │
    │                                - name                 │ Available:   │
    └── other-skill/                 - description          │ • drawing-   │
        └── SKILL.md                 - path                 │   memory     │
                                                            └──────────────┘
                                                                   │
                                                                   │ Agent sees
                                                                   │ catalog
                                                                   ▼
                                                            [Agent has metadata
                                                             for all skills,
                                                             but NOT full content]
```

---

## Phase 2: Skill Loading Workflow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ USER MESSAGE TRIGGERS SKILL                                                  │
└─────────────────────────────────────────────────────────────────────────────┘

User: "Remember that CODE-ALPHA7 is the emergency code"
  │
  │
  ▼
┌──────────────────────────────────────────────────────────────────────────┐
│ AGENT (Cloud or Local)                                                   │
│                                                                          │
│ 1. Checks `skills` memory block:                                        │
│    ✓ "drawing-memory" available                                         │
│    ✓ Description matches task (storing information)                     │
│                                                                          │
│ 2. Decision: "I should use the drawing-memory skill"                    │
│                                                                          │
│ 3. Calls Tool:                                                           │
│    Skill(command="load", skills=["drawing-memory"])                     │
└──────────────────────────────────────────────────────────────────────────┘
  │
  │ Tool call sent to client
  │
  ▼
┌──────────────────────────────────────────────────────────────────────────┐
│ LETTA CLIENT (Client-Side Execution)                                    │
│                                                                          │
│ Skill tool is a STUB on server:                                         │
│   def Skill(command, skills=None):                                      │
│       raise Exception("This is a stub. Execute client-side.")           │
│                                                                          │
│ Client intercepts the call via approval flow:                           │
│                                                                          │
│ 1. Reads `skills` memory block to get directory path                    │
│    Path: .skills/                                                       │
│                                                                          │
│ 2. Constructs file path:                                                │
│    .skills/drawing-memory/SKILL.md                                      │
│                                                                          │
│ 3. Reads entire SKILL.md file (777 lines)                               │
│                                                                          │
│ 4. Appends content to `loaded_skills` memory block:                     │
│    ┌────────────────────────────────────────┐                          │
│    │ loaded_skills block (read-only)        │                          │
│    │                                         │                          │
│    │ # Drawing Memory Skill                 │                          │
│    │ ## Research Foundation                 │                          │
│    │ [Full 777 lines of SKILL.md content]   │                          │
│    │ ...                                     │                          │
│    └────────────────────────────────────────┘                          │
│                                                                          │
│ 5. Returns success to agent                                             │
└──────────────────────────────────────────────────────────────────────────┘
  │
  │ Skill now loaded in agent's context
  │
  ▼
┌──────────────────────────────────────────────────────────────────────────┐
│ AGENT (Now Has Full Skill Instructions)                                 │
│                                                                          │
│ loaded_skills block now contains complete workflow:                     │
│ • Step 1: Extract Evidence                                              │
│ • Step 2: Score Evidence (0-10)                                         │
│ • Step 3: Paraphrase                                                    │
│ • Step 4: Generate Sketch Steps (MOTOR COMPONENT)                       │
│ • Step 5-9: Create scene, validate, store                               │
│                                                                          │
│ Agent follows instructions:                                             │
│ 1. Extracts evidence from user message                                  │
│    - Quote: "CODE-ALPHA7 is the emergency code"                         │
│    - Context: Recent conversation                                       │
│                                                                          │
│ 2. Scores evidence: 6 points (MEDIUM)                                   │
│                                                                          │
│ 3. Generates own sketch steps:                                          │
│    1) Draw red emergency phone                                          │
│    2) Add gold star by dial                                             │
│    3) Person reaching urgently                                          │
│    4) Speech bubble: "CODE-ALPHA7"                                      │
│    5) Label "Emergency Code"                                            │
│                                                                          │
│ 4. Creates scene text with metaphor + mark                              │
│                                                                          │
│ 5. Stores in `human` memory block:                                      │
│    [FACT] CODE-ALPHA7 is emergency code (confidence: MED)               │
│    [SCENE] Red emergency phone with gold star...                        │
│                                                                          │
│ 6. Unloads skill when done:                                             │
│    Skill(command="unload", skills=["drawing-memory"])                   │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Memory Blocks Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ AGENT MEMORY BLOCKS                                                          │
└─────────────────────────────────────────────────────────────────────────────┘

┌────────────────────────────────┐
│ skills (read-only)             │  ← Populated at startup by client
│ Size: ~500 words               │
│ Always in context              │  Purpose: Catalog of available skills
├────────────────────────────────┤
│ Skills Directory:              │
│ /path/to/.skills               │
│                                │
│ Available Skills:              │
│                                │
│ ### drawing-memory             │  ← Agent sees this and knows
│ ID: drawing-memory             │    when to load the skill
│ Description: Store facts...    │
│                                │
│ ### other-skill                │
│ ID: other-skill                │
│ Description: ...               │
└────────────────────────────────┘

            ↓ When agent calls Skill(load)
            
┌────────────────────────────────┐
│ loaded_skills (read-only)      │  ← Updated by Skill tool (client-side)
│ Size: Variable (grows)         │
│ Only loaded skills in context  │  Purpose: Full instructions for active skills
├────────────────────────────────┤
│                                │
│ [Currently empty]              │  ← Before loading
│                                │
└────────────────────────────────┘

            ↓ After Skill(load, ["drawing-memory"])
            
┌────────────────────────────────┐
│ loaded_skills (read-only)      │
│ Size: ~26KB (777 lines)        │
│ In context WHILE skill active  │
├────────────────────────────────┤
│ # Drawing Memory Skill         │  ← Complete SKILL.md content
│                                │
│ ## Research Foundation         │  ← Agent can now read and
│ ...                            │    follow all instructions
│ ## Step 1: Extract Evidence    │
│ ## Step 2: Score Evidence      │
│ ## Step 3: Paraphrase          │
│ ## Step 4: Generate Sketch     │  ← MOTOR COMPONENT
│ ...                            │
│ [Complete 777-line workflow]   │
└────────────────────────────────┘

            ↓ When agent calls Skill(unload)
            
┌────────────────────────────────┐
│ loaded_skills (read-only)      │
│ Size: 0                        │
├────────────────────────────────┤
│ [Currently empty]              │  ← Content removed, context freed
└────────────────────────────────┘
```

---

## Progressive Disclosure in Action

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ CONTEXT WINDOW EFFICIENCY                                                    │
└─────────────────────────────────────────────────────────────────────────────┘

BEFORE skill is needed:
┌────────────────────────────────────────────────────┐
│ Agent Context Window (200K tokens)                 │
├────────────────────────────────────────────────────┤
│ System Prompt            5K tokens                 │
│ Conversation History    50K tokens                 │
│ Memory Blocks:                                     │
│   - human               10K tokens                 │
│   - persona              2K tokens                 │
│   - skills               1K tokens  ← Metadata only│
│   - loaded_skills        0K tokens  ← Empty!       │
├────────────────────────────────────────────────────┤
│ Available: 132K tokens                             │
└────────────────────────────────────────────────────┘

AFTER loading drawing-memory skill:
┌────────────────────────────────────────────────────┐
│ Agent Context Window (200K tokens)                 │
├────────────────────────────────────────────────────┤
│ System Prompt            5K tokens                 │
│ Conversation History    50K tokens                 │
│ Memory Blocks:                                     │
│   - human               10K tokens                 │
│   - persona              2K tokens                 │
│   - skills               1K tokens  ← Still just   │
│   - loaded_skills       13K tokens  ← Full content │
├────────────────────────────────────────────────────┤
│ Available: 119K tokens                             │
└────────────────────────────────────────────────────┘

Cost: 13K tokens (only when needed)
Benefit: Agent has complete 9-step workflow with examples

AFTER unloading skill:
┌────────────────────────────────────────────────────┐
│ Agent Context Window (200K tokens)                 │
├────────────────────────────────────────────────────┤
│ System Prompt            5K tokens                 │
│ Conversation History    50K tokens                 │
│ Memory Blocks:                                     │
│   - human               10K tokens                 │
│   - persona              2K tokens                 │
│   - skills               1K tokens                 │
│   - loaded_skills        0K tokens  ← Freed!       │
├────────────────────────────────────────────────────┤
│ Available: 132K tokens                             │
└────────────────────────────────────────────────────┘

Space recovered: 13K tokens available for other uses
```

---

## Comparison: Tools vs Skills

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ TOOL APPROACH (process_memory_v4)                                           │
└─────────────────────────────────────────────────────────────────────────────┘

Agent: I need to store a fact
  │
  ▼
┌────────────────────────────────────────────┐
│ Tool Schema (always in context)           │  ← ~1K tokens always loaded
│                                            │
│ process_memory_v4(                         │
│   fact_content: str,                       │
│   evidence_json: str,  ← Rigid format      │
│   context: str,                            │
│   frequency: int,      ← 8 parameters      │
│   importance_base: int,                    │
│   risk_if_wrong: int,                      │
│   depiction_hint: str,                     │
│   persist_to_archival: bool                │
│ )                                          │
└────────────────────────────────────────────┘
  │
  │ Agent must format JSON correctly
  │
  ▼
┌────────────────────────────────────────────┐
│ Server-Side Execution                      │
│                                            │
│ Python code runs (553 lines)              │
│ - Deterministic logic                      │
│ - Auto-generates sketch steps  ← PASSIVE  │
│ - Returns JSON result                      │
└────────────────────────────────────────────┘
  │
  ▼
Agent receives result, must parse JSON

Problems:
❌ Always in context (even when not needed)
❌ Rigid schema (8 required parameters)
❌ JSON formatting burden
❌ Agent is passive (no motor component)
❌ No retrieval guidance
❌ Hard to iterate (need to redeploy)


┌─────────────────────────────────────────────────────────────────────────────┐
│ SKILL APPROACH (drawing-memory)                                             │
└─────────────────────────────────────────────────────────────────────────────┘

Agent: I need to store a fact
  │
  ▼
┌────────────────────────────────────────────┐
│ Skill Catalog (always in context)         │  ← ~200 words (~1K tokens)
│                                            │
│ drawing-memory: Store facts with          │
│ evidence using drawing effect...          │
└────────────────────────────────────────────┘
  │
  │ Agent decides to load skill
  │
  ▼
┌────────────────────────────────────────────┐
│ Skill(command="load",                      │
│       skills=["drawing-memory"])           │
└────────────────────────────────────────────┘
  │
  │ Client loads full content
  │
  ▼
┌────────────────────────────────────────────┐
│ loaded_skills block (13K tokens)          │
│                                            │
│ Complete natural language instructions:   │
│ • Extract evidence naturally              │
│ • Score flexibly (5-7 OK)                 │
│ • Generate your own sketch steps  ← ACTIVE│
│ • Create scene text                       │
│ • Store in memory                         │
│ • Reconstruct at retrieval                │
│                                            │
│ + 4 worked examples                       │
│ + Troubleshooting guide                   │
│ + Edge case handling                      │
└────────────────────────────────────────────┘
  │
  │ Agent follows guidelines
  │ Agent GENERATES sketch steps
  │ Agent stores directly in memory
  │
  ▼
┌────────────────────────────────────────────┐
│ Skill(command="unload",                    │
│       skills=["drawing-memory"])           │
└────────────────────────────────────────────┘
  │
  ▼
Context freed (13K tokens back)

Benefits:
✅ Only in context when needed (progressive disclosure)
✅ Natural language (no rigid schema)
✅ No JSON formatting
✅ Agent is ACTIVE (generates sketch steps)
✅ Includes retrieval guidance
✅ Easy to iterate (just edit SKILL.md)
```

---

## Client-Side vs Server-Side Execution

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ WHERE CODE RUNS                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

SERVER-SIDE (Letta Cloud / API):
┌────────────────────────────────────────────┐
│ • Agent reasoning (LLM calls)              │
│ • Standard tools (memory, search)          │
│ • process_memory_v4 tool (Python code)     │
│ • Tool schemas and descriptions            │
│ • Memory block storage                     │
└────────────────────────────────────────────┘
        ▲                    │
        │                    ▼
      API calls        Responses
        │                    │
        └────────────────────┘
                 │
                 │ Network
                 │
┌────────────────────────────────────────────┐
│ CLIENT-SIDE (Letta Code CLI / Your Computer)│
│                                            │
│ • Skill tool execution (stub on server)   │
│ • File system access (.skills/ directory) │
│ • SKILL.md reading and parsing            │
│ • loaded_skills block updates             │
│ • Approval flow handling                  │
└────────────────────────────────────────────┘
        ▲
        │ Direct filesystem access
        ▼
┌────────────────────────────────────────────┐
│ .skills/drawing-memory/                    │
│   ├── SKILL.md                             │
│   ├── references/                          │
│   └── examples/                            │
└────────────────────────────────────────────┘
```

---

## Complete End-to-End Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ COMPLETE WORKFLOW: User Message → Skill Execution → Memory Storage          │
└─────────────────────────────────────────────────────────────────────────────┘

1. STARTUP
   │
   ├─ Letta Code scans .skills/ directory
   ├─ Parses SKILL.md frontmatter (name, description)
   ├─ Creates skills memory block with catalog
   └─ Agent has metadata (200 words), NOT full content

2. USER MESSAGE: "Remember that CODE-ALPHA7 is the emergency code"
   │
   └─ Sent to agent (cloud or local)

3. AGENT REASONING
   │
   ├─ Checks skills block: "drawing-memory available"
   ├─ Description matches: "Store facts with evidence..."
   └─ Decision: "I should load this skill"

4. AGENT CALLS TOOL
   │
   └─ Skill(command="load", skills=["drawing-memory"])

5. CLIENT INTERCEPTS (Approval Flow)
   │
   ├─ Reads skills block for directory path
   ├─ Constructs path: .skills/drawing-memory/SKILL.md
   ├─ Reads entire file (777 lines)
   ├─ Appends to loaded_skills memory block
   └─ Returns success

6. AGENT NOW HAS FULL INSTRUCTIONS
   │
   ├─ Reads loaded_skills block
   ├─ Sees 9-step workflow
   └─ Follows instructions

7. AGENT EXECUTES WORKFLOW
   │
   ├─ Step 1: Extract evidence
   │   └─ Quote: "CODE-ALPHA7 is the emergency code"
   │
   ├─ Step 2: Score evidence
   │   └─ Score: 6 points (MEDIUM)
   │
   ├─ Step 3: Paraphrase
   │   └─ "CODE-ALPHA7 is defined as the emergency code"
   │
   ├─ Step 4: GENERATE SKETCH STEPS (Motor component!)
   │   └─ Agent creates:
   │       1) Draw red emergency phone
   │       2) Add gold star by dial
   │       3) Person reaching urgently
   │       4) Speech bubble: "CODE-ALPHA7"
   │       5) Label "Emergency Code"
   │
   ├─ Step 5-7: Create scene with metaphor + mark
   │   └─ Scene: "Red emergency phone with gold star..."
   │
   ├─ Step 8: Validate (no new claims)
   │   └─ ✓ CODE-ALPHA7 is in evidence
   │
   └─ Step 9: Store in human memory block
       └─ [FACT] CODE-ALPHA7... (confidence: MED)
           [SCENE] Red emergency phone...

8. AGENT UNLOADS SKILL
   │
   └─ Skill(command="unload", skills=["drawing-memory"])

9. CLIENT UNLOADS
   │
   ├─ Removes content from loaded_skills block
   └─ Frees 13K tokens of context

10. LATER: USER ASKS "What was that emergency code?"
    │
    ├─ Agent searches human memory block
    ├─ Finds: [FACT] + [SCENE]
    │
    ├─ Agent RECONSTRUCTS SCENE (Retrieval component!)
    │   └─ "Red emergency phone, gold star, person reaching..."
    │
    └─ Agent answers with context:
        "The emergency code is CODE-ALPHA7. I remember this from 
        our earlier conversation - I created a sketch with a red 
        emergency phone and a gold star to mark it as critical."
```

---

## Key Advantages of Skills Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ WHY SKILLS > TOOLS FOR DRAWING MEMORY                                       │
└─────────────────────────────────────────────────────────────────────────────┘

1. PROGRESSIVE DISCLOSURE
   Tools: Always in context (wastes tokens)
   Skills: Load only when needed (saves 13K tokens)

2. NATURAL LANGUAGE
   Tools: Rigid schema (8 required parameters, JSON formatting)
   Skills: Flexible guidelines ("aim for 3 quotes, but 2 is OK if detailed")

3. AGENT IS ACTIVE
   Tools: Auto-generates sketch steps (agent is passive)
   Skills: Agent generates sketch steps (motor component works!)

4. RETRIEVAL GUIDANCE
   Tools: No guidance on how to use scenes for recall
   Skills: Complete retrieval workflow (reconstruct scene before answering)

5. RICH EXAMPLES
   Tools: No examples
   Skills: 4 examples in SKILL.md + 10 edge cases in references/

6. EASY ITERATION
   Tools: Edit Python, redeploy to cloud, test (30 min cycle)
   Skills: Edit SKILL.md locally, test immediately (5 min cycle)

7. FLEXIBLE THRESHOLDS
   Tools: score >= 8 required (rigid)
   Skills: score 5-7 OK with judgment (flexible)

8. TROUBLESHOOTING
   Tools: No troubleshooting guidance
   Skills: Complete troubleshooting section with solutions

9. BUNDLED RESOURCES
   Tools: Standalone function only
   Skills: SKILL.md + references/ + examples/ (progressive disclosure)

10. RESEARCH-BACKED
    Tools: Implements logic, but agent doesn't DO the work
    Skills: Agent GENERATES (motor) and RECONSTRUCTS (retrieval) = drawing effect
```

---

## Summary

**Skills in Letta use:**
1. **Two memory blocks**: `skills` (catalog) and `loaded_skills` (content)
2. **Client-side execution**: Skill tool is stub on server, executes on client
3. **Filesystem access**: Client reads .skills/ directory directly
4. **Progressive disclosure**: Only load full content when needed
5. **Natural language**: Agent follows flexible guidelines, not rigid schemas
6. **Active generation**: Agent creates sketch steps (motor component)
7. **Retrieval support**: Agent reconstructs scenes (retrieval component)

**The key insight**: Skills make the agent ACTIVE (generating, reconstructing) rather than PASSIVE (calling a function that does everything). This aligns with the research showing the drawing effect requires doing the work yourself!
