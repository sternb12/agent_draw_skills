# CLAUDE.md

Guidance for AI coding assistants working in this repository.

## What This Repository Is

A dual-trace memory encoding skill for letta_v1_agent, empirically validated
on LongMemEval-S (LME-S). The C6 condition (dual-trace) achieved 73.7% overall
accuracy vs 54% for the fact-only control (C7), a +19.7 percentage point gain.

The approach is inspired by the drawing effect (Fernandes et al., 2018) and
works by forcing elaborative encoding: each piece of information is stored as
two linked archival passages -- a structured fact trace and a vivid scene trace
with concrete imageable detail. The mechanism is elaborative generation, not
motor encoding. The agent generates the scene text itself, which produces richer
encoding than receiving pre-computed output, but this is not equivalent to the
motor component of human drawing (visuospatial planning, fine motor control,
perceptual feedback). Do not claim or imply motor equivalence.

## Architecture

Agent type:  letta_v1_agent
Storage:     archival memory (archival_memory_insert / archival_memory_search)
Format:      [FACT:anchor] + [SCENE:anchor] text tags in a single passage
No file system access, no Skill tool loading, no memory blocks beyond persona.

## Repository Structure

  README.md                              -- quick start, results, architecture
  skills/drawing-memory/SKILL.md         -- encoding + retrieval instructions
  docs/ade-quick-start.md                -- ADE persona for non-SDK users
  skills/drawing-memory/references/
    worked-examples.md                   -- 3 annotated LME-S examples
    token-analysis.md                    -- C6 vs C7 token cost comparison
    evidence-scoring-details.md          -- extended scoring guidance
    edge-cases.md                        -- edge case handling

## Key Constraints

- All files must be ASCII only -- no unicode, no emoji
- No local file paths (use /path/to/your/agent_draw_skills as placeholder)
- No personal names in documentation
- SKILL.md and README.md must stay internally consistent
- Model: anthropic/claude-sonnet-4-6 was used in the evaluation -- always
  specify this when describing results
