# Token Usage: Dual-Trace vs Fact-Only

Measured on LongMemEval-S recall evaluation: 100 questions each condition,
same agent model (Claude Sonnet), same 4,575-session teach corpus.

## Per-Question Token Usage

  Metric                     C6 (dual-trace)   C7 (fact-only)      Delta
  -----------------------------------------------------------------------
  Prompt tokens/question         243,910          252,465     -8,555 (-3.4%)
  Completion tokens/question         474              284       +190 (+66.9%)
  Cached tokens/question         197,410          185,518    +11,892 (+6.4%)
  Total tokens/question          244,385          252,749     -8,364 (-3.3%)

  Cache hit rate:                  80.9%            73.5%

## Key Findings

1. DUAL-TRACE USES FEWER TOTAL TOKENS AT INFERENCE TIME (-3.3%)

   C6 has more passages than C7 (2,996 vs 2,888) but costs less per recall
   question. The reason is the cache hit rate: 80.9% vs 73.5%. Scene passages
   were already warm in the KV cache when recall questions arrived. More
   richly encoded context leads to more cache hits, not more cost.

2. THE ONLY OVERHEAD IS COMPLETION TOKENS (+67%)

   C6 generates approximately 190 more output tokens per question. The agent's
   answers are richer because they draw on scene content -- specific details,
   contextual framing, temporal anchors. This is the cost of better answers,
   not a retrieval overhead.

3. SCENE ENCODING COST IS ONE-TIME, NOT PER-QUERY

   Writing scene files during the teach phase costs more tokens than writing
   fact files alone. But that cost is paid once per session at encoding time.
   Every subsequent recall question benefits from the richer encoding at no
   additional prompt-token cost. The economics improve with use.

## Summary

   Dual-trace encoding is +19.7 percentage points more accurate
   and 3.3% cheaper per query than fact-only encoding.

   The accuracy gain does not come with a token tax. The scene files,
   once encoded, are cached efficiently and retrieved at lower marginal
   cost than equivalent-volume fact-only content.

## Methodology Note

   Token counts are from letta-evals results.jsonl (prompt_tokens,
   completion_tokens, cached_input_tokens fields). Dollar cost was not
   captured in the evaluation results. Cache hit rates reflect KV cache
   reuse within the recall evaluation run; rates in production deployments
   will vary with query patterns and session timing.
