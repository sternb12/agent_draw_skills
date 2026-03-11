# Drawing Memory Skill

## Overview

This skill implements the "drawing effect" from cognitive science research to improve agent memory through integrated elaborative processing, motor generation, and pictorial inspection.

## Research Foundation

Based on Fernandes et al. (2018) findings that drawing beats writing, visualization alone, or viewing pictures because it creates an integrated memory trace with three components:

1. **Elaborative/Semantic processing** - Understanding and transforming
2. **Motor component** - Actively generating, not just reading  
3. **Pictorial inspection** - Visual checking and verification

## Files

- **SKILL.md** (777 lines) - Main skill instructions with complete workflow
- **references/evidence-scoring-details.md** (208 lines) - Detailed evidence scoring formulas and edge cases
- **examples/edge-cases.md** (423 lines) - 10 complete examples covering special situations

## Key Features

### Critical Components (Research-Backed)

1. **Motor Component** - Agent generates sketch steps (not auto-generated)
2. **Retrieval Guidance** - Scene reconstruction for context reinstatement
3. **Flexible Thresholds** - Guidelines over rigid rules (5-7 acceptable, not just 8+)
4. **Natural Language** - No JSON formatting required
5. **Expanded Metaphor Catalog** - 50+ options organized by category

### Improvements Over Tool Approach

| Tool Weakness | Skill Solution |
|---------------|----------------|
| Rigid threshold (8+) -> 71% TENTATIVE | Flexible thresholds (5-7 OK) |
| JSON format required | Natural evidence identification |
| Agent passive (auto-generated) | Agent active (generates sketch steps) |
| No retrieval guidance | Scene reconstruction at recall |
| Small metaphor menu (7 options) | Expanded catalog (50+ options) |
| No borderline guidance | Explicit edge case handling |
| No examples | 13 worked examples with reasoning |

## Usage

### For Letta Agents

1. **Discovery**: Letta automatically scans `.skills/` directory on startup
2. **Activation**: Agent loads skill when user shares information to remember
3. **Encoding**: Agent follows 9-step workflow to create memory trace
4. **Retrieval**: Agent reconstructs scenes when answering questions

### Expected Performance

**Compared to tool (process_memory_v4):**
- Processing rate: 70% -> 90%+ (more flexible)
- ACTIVE rate: 29% -> 70%+ (better thresholds)
- Recall accuracy: Improved (scene-based context)

**Compared to no drawing (control):**
- Exact recall: +15-25% expected
- Keyphrase recall: +10-20% expected
- Foil rejection: +10-15% expected

## Workflow Summary

### ENCODING (When Storing)

1. Extract evidence (quotes, context, source, timestamp)
2. Score evidence (0-10, flexible thresholds)
3. Paraphrase (elaboration component)
4. **Generate sketch steps** (motor component) ⭐
5. Choose metaphor (vary to avoid interference)
6. Add distinctive mark
7. Create scene text
8. Validate (no new claims)
9. Store in `human` block

### RETRIEVAL (When Answering)

1. Search for relevant facts
2. **Reconstruct scene** (context reinstatement) ⭐
3. Use scene as retrieval cue
4. Answer with fact + scene context

## Critical Success Factors

[x] Agent generates sketch steps (motor component)  
[x] Agent reconstructs scenes at retrieval  
[x] Vary metaphors (distinctiveness)  
[x] Use flexible thresholds (judgment over rules)  

## Testing Plan

### Phase 1: Single Example Test
```bash
letta --new
# Agent discovers skill automatically
# Test: "Remember that CODE-ALPHA7 is the emergency code"
# Verify: Agent loads skill, generates sketch steps, stores correctly
```

### Phase 2: A/B Comparison
- **Skill-Agent**: Has drawing-memory skill
- **Tool-Agent**: Uses process_memory_v4 tool  
- **NoDraw-Agent**: Standard memory only

Compare on teach/recall datasets (40 teach + 30 recall samples)

### Success Criteria
- Processing rate > 85%
- ACTIVE rate > 60%
- Exact recall > NoDraw baseline
- Agent reasoning shows motor generation

## References

- Fernandes, M. A., et al. (2018). The Surprisingly Powerful Influence of Drawing on Memory. *Current Directions in Psychological Science*.
- Agent Skills specification: https://agentskills.io/
- Letta Skills documentation: https://docs.letta.com/letta-code/skills/

## Version History

- **v1.0** (2026-01-06) - Initial implementation
  - Complete 9-step encoding workflow
  - Retrieval guidance with scene reconstruction
  - 50+ metaphor catalog
  - Flexible evidence thresholds
  - 13 worked examples
  - Research-backed motor and retrieval components

## License

Proprietary - Part of drawing-on-memory research project
