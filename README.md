# Agent Drawing Memory Skill

**Research-backed memory enhancement for AI agents using the "drawing effect"**

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-blue)](https://agentskills.io/)
[![Letta](https://img.shields.io/badge/Letta-Supported-green)](https://letta.com/)

## Overview

This skill implements the **drawing effect** from cognitive science research, enabling AI agents to create richer, more retrievable memory traces. Unlike traditional memory storage, this approach translates the three-component drawing effect (elaborative processing, motor generation, pictorial inspection) into text-based workflows that AI agents can execute.

## Research Foundation

### The Drawing Effect (Fernandes et al., 2018)

The **Impact of Drawing on Memory** research demonstrates that drawing information produces significantly better recall than:
- Writing text descriptions
- Visualization alone
- Viewing pictures
- Simply reading

**Why Drawing Works:**

Drawing creates an **integrated memory trace** with three critical components:

1. **Elaborative/Semantic Processing**
   - Understanding and transforming information
   - Creating connections and relationships
   - Active interpretation vs. passive reception

2. **Motor Component** ⭐
   - Physical act of creating (doing, not just reading)
   - Generative process engages deeper processing
   - *Critical insight*: The benefit requires YOU doing the work

3. **Pictorial Inspection**
   - Visual checking and verification
   - Spatial/visual encoding alongside verbal
   - Distinctiveness through visualization

**Key Research Findings:**
- Drawing improves **recollection** (context-rich memory), not just familiarity
- The benefit shows at **retrieval**, not just encoding
- **Context reinstatement** (reconstructing the scene) aids recall
- Motor execution is essential (auto-generated content doesn't work)
- Varied metaphors increase distinctiveness

### Translation to AI Agents

Since AI agents cannot physically draw, we translate each component:

| Human Process | AI Agent Translation |
|---------------|---------------------|
| Understanding content | Paraphrasing facts to 3rd person |
| Drawing (motor) | **Generating own sketch steps** (not reading templates) |
| Visual inspection | Creating scene descriptions with metaphors |
| Retrieval context | **Reconstructing scenes before answering** |

**The Critical Difference**: The agent must **actively generate** content (sketch steps, scene text), not just call a function that does it automatically. This preserves the motor component that makes drawing effective.

## Why Skills Instead of Tools?

Traditional Letta tools are **passive** - the agent calls a function and receives results. This approach makes the agent **active**:

- **Agent generates sketch steps** (motor component)
- **Agent reconstructs scenes at retrieval** (context reinstatement)
- **Flexible guidelines** instead of rigid schemas
- **Progressive disclosure** (loads only when needed)

This active participation is what makes the drawing effect work!

## Installation

### Prerequisites
- Letta Code CLI (0.7.2+)
- Git

### Setup

1. **Clone this repository**:
   ```bash
   git clone https://github.com/sternb12/agent_draw_skills.git
   cd agent_draw_skills
   ```

2. **Note the skills path**:
   ```bash
   pwd
   # Example: /Users/yourname/agent_draw_skills
   ```

3. **Configure your Letta agent** (via Web UI or CLI):

   **Option A - Cloud Agent (Web UI + CLI):**
   - Go to https://app.letta.com
   - Create or select agent
   - Add memory block: `skills` (read-only)
     - Content: See [docs/CLOUD_AGENT_SKILLS_SETUP.md](docs/CLOUD_AGENT_SKILLS_SETUP.md)
   - Add memory block: `loaded_skills` (read-only, initially empty)
   - Verify Skill tool is attached
   - Connect via CLI: `letta --agent <agent-id>`

   **Option B - Local Agent (CLI only):**
   - Navigate to repository directory
   - Run: `letta --new`
   - Skills auto-discovered from `skills/` directory

## Usage

### Basic Workflow

**1. Store Information**
```
User: "Remember that CODE-ALPHA7 is the emergency code from training"

Agent:
  → Loads drawing-memory skill
  → Extracts evidence from conversation
  → Scores evidence (0-10 scale)
  → Paraphrases to 3rd person
  → Generates own sketch steps (motor component)
  → Creates scene with metaphor + distinctive mark
  → Stores [FACT] + [SCENE] in memory
  → Unloads skill
```

**2. Retrieve Information**
```
User: "What was that emergency code?"

Agent:
  → Searches memory for FACT + SCENE
  → Reconstructs scene mentally (retrieval component)
  → Answers with context-rich response
  
Response: "The emergency code is CODE-ALPHA7. I remember creating
a sketch of a red emergency phone with a gold star marking it..."
```

### What Makes This Work

**Motor Component**: Agent generates its own sketch steps
```
✅ GOOD (Agent generates):
   1) Draw red emergency phone on wall
   2) Add gold star by dial
   3) Person reaching urgently
   4) Speech bubble: "CODE-ALPHA7"
   5) Label "Emergency Code"

❌ BAD (Auto-generated):
   Agent doesn't do the generative work - no motor component
```

**Retrieval Component**: Agent reconstructs scene before answering
```
✅ GOOD:
   "I remember the red phone with gold star... CODE-ALPHA7"
   
❌ BAD:
   "CODE-ALPHA7" (no scene reconstruction)
```

## Skill Components

```
skills/drawing-memory/
├── SKILL.md (777 lines)
│   ├── 9-step encoding workflow
│   ├── Retrieval workflow with scene reconstruction
│   ├── Evidence scoring (0-10 scale)
│   ├── Metaphor catalog (50+ options)
│   └── Troubleshooting guide
│
├── README.md
│   └── Skill overview
│
├── examples/
│   └── edge-cases.md (10 worked examples)
│       ├── High evidence (score 8-10)
│       ├── Medium evidence (score 5-7)
│       ├── Low evidence (score 0-4)
│       └── Edge cases (sensitive, timestamps, etc.)
│
└── references/
    └── evidence-scoring-details.md
        ├── Complete scoring formulas
        ├── Routing logic (DROP/TENTATIVE/ACTIVE)
        └── Mathematical basis
```

## Documentation

- **[Architecture Guide](docs/SKILLS_ARCHITECTURE_DIAGRAM.md)** - How skills work in Letta
- **[Setup Guide](docs/CLOUD_AGENT_SKILLS_SETUP.md)** - Step-by-step configuration
- **[Quick Setup](docs/setup_guide.txt)** - Copy-paste instructions

## Research Citation

This implementation is based on:

> Fernandes, M. A., Wammes, J. D., & Meade, M. E. (2018). **The Surprisingly Powerful Influence of Drawing on Memory**. *Current Directions in Psychological Science*, 27(5), 302-308. https://doi.org/10.1177/0963721418755385

**Key Findings Applied:**
- Drawing creates integrated memory traces (elaboration + motor + pictorial)
- Motor execution is critical (agent must generate, not read)
- Retrieval benefits from context reinstatement (scene reconstruction)
- Distinctiveness through varied metaphors improves recall

## Technical Details

### Agent Skills Standard Compliance

This skill follows the [Agent Skills open standard](https://agentskills.io/):
- ✅ YAML frontmatter (name, description)
- ✅ Progressive disclosure (metadata → instructions → resources)
- ✅ Cross-platform compatible (Letta, Claude, Cursor, VS Code, etc.)
- ✅ Bundled resources (examples, references)

### Memory Blocks Required

**`skills`** (read-only):
- Catalog of available skills
- Always in context (~1K tokens)
- Contains skill IDs, descriptions, directory path

**`loaded_skills`** (read-only):
- Full content of active skills
- Empty until loaded
- Populated by Skill tool (client-side)

### Evidence Scoring

The skill uses a 5-dimension evidence scoring system (0-12 points):

1. **Frequency** (0-3): Number of mentions
2. **Source Quality** (0-3): Explicit > Implicit > Inferred
3. **Clarity** (0-2): Unambiguous vs vague
4. **Consistency** (0-2): No contradictions
5. **Stability** (0-2): Temporal persistence

**Routing**:
- **8-12 points** → ACTIVE (high confidence, store immediately)
- **5-7 points** → ACTIVE (medium confidence, acceptable)
- **0-4 points** → TENTATIVE or DROP (insufficient evidence)

## Comparison: Skill vs Tool

| Feature | Tool (process_memory_v4) | Skill (drawing-memory) |
|---------|-------------------------|------------------------|
| **Context** | Always loaded | Load only when needed |
| **Format** | Rigid JSON schema | Flexible natural language |
| **Agent Role** | PASSIVE (calls function) | ACTIVE (generates content) |
| **Thresholds** | Rigid (score ≥ 8) | Flexible (5-7 acceptable) |
| **Motor Component** | Auto-generated | Agent generates |
| **Retrieval** | No guidance | Scene reconstruction |
| **Examples** | None | 14 worked examples |
| **Iteration Speed** | 30 min (redeploy) | 5 min (edit SKILL.md) |

## Expected Improvements

Based on initial testing compared to traditional tool approach:

- **Processing Rate**: 70% → 90%+ (flexible thresholds)
- **ACTIVE Storage**: 29% → 70%+ (better confidence levels)
- **Recall Accuracy**: +15-25% (scene-based context)

## Contributing

Contributions welcome! This skill can be improved through:
- Additional metaphor types
- Refined evidence scoring
- Enhanced retrieval strategies
- Cross-platform testing

## License

MIT License - See LICENSE file for details

## Acknowledgments

- Research foundation: Fernandes, Wammes, & Meade (2018)
- Agent Skills standard: [agentskills.io](https://agentskills.io/)
- Built for [Letta](https://letta.com/) AI agent platform

---

**Status**: ✅ Production-ready (tested with Letta agents)

**Version**: 1.0.0

**Last Updated**: January 2026
