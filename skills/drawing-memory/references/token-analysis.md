# Token Usage: Dual-Trace vs Fact-Only

   Dual-trace encoding was 3.3% cheaper per query than fact-only encoding.

   The scene files,
   once encoded, are cached efficiently and retrieved at lower marginal
   cost than equivalent-volume fact-only content.

## Methodology Note

   Token counts are from letta-evals results.jsonl (prompt_tokens,
   completion_tokens, cached_input_tokens fields). Dollar cost was not
   captured in the evaluation results. Cache hit rates reflect KV cache
   reuse within the recall evaluation run; rates in production deployments
   will vary with query patterns and session timing.
