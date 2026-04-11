# Token Usage: Dual-Trace vs Fact-Only

Dual-trace encoding is cheaper than fact-only encoding at both teach time
and recall time.

## Encoding (teach phase)

Dual-trace encoding was 1.7% cheaper per session than fact-only encoding.
Although scenes add completion tokens at encoding time, the structured format
produces more compact archival passages overall. Teach phase rates:

  C6 dual-trace: 4,375 sessions in 4.1 hours (17.6 sessions/min, 0 restarts)
  C7 fact-only:  4,575 sessions in ~4.2 hours (multiple restarts on final 200)

## Retrieval (recall phase)

Dual-trace encoding was 3.3% cheaper per query than fact-only encoding across
the 100-question evaluation.

Per-query token breakdown (averages across 100 recall questions):

  Total prompt tokens per query:
    C6 dual-trace: ~244,385 tokens
    C7 fact-only:  ~252,750 tokens

  KV cache hit rate:
    C6 dual-trace: 80.9%
    C7 fact-only:  73.5%

  Non-cached (full-rate) prompt tokens per query:
    C6 dual-trace: ~46,700 tokens   (244,385 x 0.191)
    C7 fact-only:  ~66,900 tokens   (252,750 x 0.265)

  Cached (reduced-rate) prompt tokens per query:
    C6 dual-trace: ~197,700 tokens  (244,385 x 0.809)
    C7 fact-only:  ~185,800 tokens  (252,750 x 0.735)

C6 sends ~20,200 fewer non-cached tokens per query than C7. Over the full
100-question run this is ~2.03M non-cached tokens (4.65M vs 6.69M total).
This matters because non-cached tokens cost roughly 10x more than cache-read
tokens at standard Sonnet pricing.

The higher cache hit rate in C6 reflects that scene passages -- once warm in
the KV cache from the teach phase -- are re-used efficiently across multiple
recall queries. Scene content, being concrete and distinctive, tends to remain
cache-resident longer than equivalent-volume fact content.

## Dollar Cost Estimate (Claude Sonnet 4.6 rates)

At Sonnet 4.6 pricing ($3/M input, $15/M output, $0.30/M cache read),
for the 100-question evaluation run:

  C6 dual-trace: ~$20.58 total ($0.21 per question)
  C7 fact-only:  ~$26.08 total ($0.26 per question)
  Savings:        ~$5.49 (-21%)

Cost breakdown per query (approximate):
                       C6          C7
  Non-cached input:  $0.140      $0.201     (46.7K / 66.9K tokens x $3/M)
  Cached input:      $0.059      $0.056     (197.7K / 185.8K tokens x $0.30/M)
  Output:            $0.007      $0.004     (~467 / ~293 completion tokens x $15/M)
  Total per query:   $0.206      $0.261

The cost advantage comes almost entirely from the cache bucket shift: C6 moves
~20,200 tokens per query from the $3/M non-cached rate to the $0.30/M cached
rate. The additional completion tokens from richer C6 answers (~174 more per
query) are a small fraction of this saving.

## Summary

  Encoding:  C6 is 1.7% cheaper per session
  Retrieval: C6 is 3.3% cheaper per query, ~21% cheaper in dollar terms
  Accuracy:  C6 is +20.2 pp more accurate overall

Dual-trace encoding delivers a 20-percentage-point accuracy gain at lower
total cost than fact-only encoding.

## Methodology Note

Token counts are from letta-evals results.jsonl (prompt_tokens,
completion_tokens, cached_input_tokens fields). Per-query totals (244,385 and
252,750) represent averages across 100 recall questions and match the values
reported in the manuscript. Non-cached token counts are derived as
prompt_tokens minus cached_input_tokens. Dollar cost computed from these counts
using Sonnet 4.6 rates (verified March 2026). Cache hit rates reflect KV cache
reuse within the recall evaluation run; rates in production deployments will
vary with query patterns and session timing.

All conditions used claude-sonnet-4-6 (anthropic/claude-sonnet-4-6) as the
agent model and text-embedding-3-small (OpenAI) as the embedding model.
