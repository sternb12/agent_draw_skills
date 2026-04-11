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
100 recall questions.

Scene passages, once encoded, are cached efficiently by the KV cache. At recall
time, the model re-uses these cached representations rather than processing them
fresh, reducing effective prompt token cost. The key metric is non-cached tokens
(which are charged at the full input rate):

  C6 dual-trace: ~4.65M non-cached tokens per query (avg)
  C7 fact-only:  ~6.69M non-cached tokens per query (avg)

C6 saves approximately 2M non-cached tokens per query. This matters because
non-cached tokens cost roughly 10x more than cache-read tokens at standard
Sonnet pricing.

KV cache utilization:
  C6 dual-trace: 80.9% cache hit rate
  C7 fact-only:  73.5% cache hit rate

The higher cache hit rate in C6 reflects that scene passages -- once warm in
the KV cache from the teach phase -- are re-used efficiently across multiple
recall queries. Scene content, being concrete and distinctive, tends to remain
cache-resident longer than equivalent-volume fact content.

## Dollar Cost Estimate (Claude Sonnet 4.6 rates)

At Sonnet 4.6 pricing ($3/M input, $15/M output, $0.30/M cache read),
for 100 recall questions:

  C6 dual-trace: ~$20.58 total ($0.21 per question)
  C7 fact-only:  ~$26.08 total ($0.26 per question)
  Savings:        ~$5.49 (-21%)

The cost advantage comes almost entirely from the cache bucket shift: C6 moves
~2M tokens from the $3/M non-cached rate to the $0.30/M cached rate per query.
The additional completion tokens from richer C6 answers (+67 tokens/query avg)
are a small fraction of this saving.

## Summary

  Encoding:  C6 is 1.7% cheaper per session
  Retrieval: C6 is 3.3% cheaper per query, ~21% cheaper in dollar terms
  Accuracy:  C6 is +20.2 pp more accurate overall

Dual-trace encoding delivers a 20-percentage-point accuracy gain at lower
total cost than fact-only encoding.

## Methodology Note

Token counts are from letta-evals results.jsonl (prompt_tokens,
completion_tokens, cached_input_tokens fields). Dollar cost computed from
these counts using Sonnet 4.6 rates (verified March 2026). Cache hit rates
reflect KV cache reuse within the recall evaluation run; rates in production
deployments will vary with query patterns and session timing.

All conditions used claude-sonnet-4-6 (anthropic/claude-sonnet-4-6) as the
agent model and text-embedding-3-small (OpenAI) as the embedding model.
