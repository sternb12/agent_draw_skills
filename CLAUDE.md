# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository implements the **Agent Drawing Memory Skill** for Letta AI agents, translating cognitive science research (the "drawing effect") into a text-based skill that AI agents can execute. The skill creates richer, more retrievable memory traces by having agents actively generate content rather than passively storing information.

## Core Architecture

### Skills vs Tools Philosophy

This implementation uses the **Agent Skills** approach rather than traditional Letta tools. The critical difference:

- **Tools (passive)**: Agent calls a function and receives results
- **Skills (active)**: Agent loads instructions, generates content itself, and actively participates

**Why this matters**: The drawing effect requires doing the work yourself. The motor component (agent generating sketch steps) and retrieval component (agent reconstructing scenes) are what make the approach effective.

### File Structure

```
skills/drawing-memory/          # The core skill implementation
+-- SKILL.md                    # Main skill instructions (555 lines)
|                              # Contains 9-step encoding workflow
|                              # Plus retrieval workflow with scene reconstruction
+-- references/                 # Progressive disclosure resources
    +-- evidence-scoring-details.md
    +-- edge-cases.md
    +-- worked-examples.md

docs/                           # Architecture & setup documentation
+-- SKILLS_ARCHITECTURE_DIAGRAM.md  # How skills work in Letta
+-- CLOUD_AGENT_SKILLS_SETUP.md     # Setup instructions
+-- drawing-memory-overview.md
+-- setup_guide.txt

README.md                       # Public-facing documentation
```

### Key Concepts

**SKILL.md Format**: Uses YAML frontmatter for metadata (name, description), followed by progressive disclosure content:
- Metadata (triggers, when to use)
- Core workflows (encoding, retrieval)
- Examples and edge cases
- Troubleshooting

**Two Memory Blocks**:
1. `skills` (read-only): Catalog of available skills (~1K tokens, always loaded)
2. `loaded_skills` (read-only): Full content when active (~13K tokens, loaded on demand)

**Client-Side Execution**: The Skill tool is a stub on the server. The Letta CLI client intercepts calls, reads SKILL.md from the filesystem, and populates the `loaded_skills` memory block.

## The Drawing Effect Translation

The skill translates three components of the drawing effect from human to AI:

1. **Elaborative Processing**: Agent paraphrases facts to 3rd person
2. **Motor Component** (critical): Agent generates its own sketch steps (not reading templates)
3. **Pictorial Inspection**: Agent creates scene descriptions with metaphors
4. **Retrieval Context**: Agent reconstructs scenes before answering questions

## Common Development Tasks

### Testing the Skill Locally

```bash
# Navigate to repository
cd /path/to/agent_draw_skills

# Start Letta CLI (auto-discovers skills from skills/ directory)
letta --new

# Test encoding
# User: "Remember that CODE-ALPHA7 is the emergency code from training"
# Agent should load skill, generate sketch steps, store [FACT] + [SCENE]

# Test retrieval
# User: "What was that emergency code?"
# Agent should reconstruct scene and answer with context
```

### Testing with Cloud Agent

```bash
# Connect to cloud agent (requires memory blocks set up in web UI)
letta --agent <agent-id>

# Skills directory must be accessible from where you run the command
# Path in `skills` memory block must match filesystem
```

### Editing the Skill

The skill content is entirely in markdown files - no code deployment needed:

1. Edit `skills/drawing-memory/SKILL.md`
2. Test immediately (just restart conversation or reload skill)
3. Iteration cycle: ~5 minutes (vs ~30 min for tool redeployment)

### Adding Examples

Add worked examples to `skills/drawing-memory/references/`:
- `worked-examples.md`: Full encoding workflows
- `edge-cases.md`: Unusual scenarios
- `evidence-scoring-details.md`: Scoring formulas and routing logic

These are loaded via progressive disclosure when the agent needs them.

## Evidence Scoring System

The skill uses a 0-10 evidence scoring system:

**Quote count**:
- 1 quote = 2 points
- 2 quotes = 4 points
- 3+ quotes = 6 points

**Quote length**:
- Total words / 12 (up to 4 points)

**Metadata**:
- Has source = +1 point
- Has timestamp = +1 point

**Routing (flexible)**:
- 8-10 points -> ACTIVE (high confidence)
- 5-7 points -> ACTIVE (medium confidence, acceptable)
- 0-4 points -> TENTATIVE or DROP

The flexibility (accepting 5-7 scores) is intentional and improves processing rates compared to rigid tool thresholds.

## Agent Skills Standard Compliance

This skill follows the [Agent Skills open standard](https://agentskills.io/):
- YAML frontmatter with name and description
- Progressive disclosure (metadata -> instructions -> resources)
- Cross-platform compatible (Letta, Claude, Cursor, VS Code)
- Bundled resources in references/ directory

## Research Foundation

Implementation based on:

> Fernandes, M. A., Wammes, J. D., & Meade, M. E. (2018). **The Surprisingly Powerful Influence of Drawing on Memory**. *Current Directions in Psychological Science*, 27(5), 302-308.

Key findings applied:
- Drawing creates integrated memory traces (elaboration + motor + pictorial)
- Motor execution is critical (agent must generate, not just read)
- Retrieval benefits from context reinstatement (scene reconstruction)
- Distinctiveness through varied metaphors improves recall

## File Editing Guidelines

When modifying skill content:

1. **SKILL.md**: Main instructions that agent follows. Keep workflows clear and sequential. Use "you" to address the agent directly. Include flexible guidelines, not rigid rules.

2. **references/**: Supporting materials loaded on demand. Can be more detailed and technical than SKILL.md.

3. **README.md**: Public documentation. Explain the "why" and research foundation for users/developers.

4. **docs/**: Setup and architecture guides. Keep paths and examples current.

## Progressive Disclosure Principle

The skill loads ~13K tokens only when needed. Structure content to minimize context usage:

- Core workflow in SKILL.md (always loaded when skill is active)
- Edge cases and details in references/ (loaded if agent needs them)
- Keep SKILL.md focused on the essential 9-step workflow

## Troubleshooting

**Agent doesn't load skill**: Check that `skills` memory block exists and contains the skill catalog with correct directory path.

**Agent loads but doesn't generate sketch steps**: The agent must be actively creating content, not just calling a function. Verify the skill instructions emphasize "YOU generate" language.

**Retrieval doesn't use scenes**: Agent should reconstruct scenes before answering. Check that retrieval workflow is clear in SKILL.md.

**Evidence scores too strict**: Scores of 5-7 are acceptable (medium confidence). The skill intentionally uses flexible thresholds.
