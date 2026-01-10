# Evidence Scoring Details

## Complete Formula

**Total Score = Quote Points + Length Points + Metadata Points**

Maximum: 10 points

### Quote Count Points (max 6)

| Number of Quotes | Points |
|------------------|--------|
| 0 quotes | 0 |
| 1 quote | 2 |
| 2 quotes | 4 |
| 3+ quotes | 6 |

### Quote Length Points (max 4)

**Formula:** `(Total words across all quotes) ÷ 12`

**Cap at 4 points**

| Total Words | Points |
|-------------|--------|
| 0-11 | 0.0-0.9 |
| 12-23 | 1.0-1.9 |
| 24-35 | 2.0-2.9 |
| 36-47 | 3.0-3.9 |
| 48+ | 4.0 |

### Metadata Points (max 2)

- Has source attribution: +1 point
- Has timestamp: +1 point

## Worked Examples

### Example 1: High Score
```
Evidence:
- Quote 1: "The deployment timeout is 300 seconds" (6 words)
- Quote 2: "This was set during the initial configuration" (7 words)
- Quote 3: "We had issues with 60 seconds before" (7 words)
- Source: User conversation on Jan 6, 2026
- Timestamp: 2026-01-06T14:30:00Z

Calculation:
- Quote count: 3 quotes = 6 pts
- Length: 20 words ÷ 12 = 1.67 pts
- Source: +1 pt
- Timestamp: +1 pt
Total: 9.67 points → HIGH confidence
```

### Example 2: Medium Score
```
Evidence:
- Quote 1: "The API key should be in the config file" (9 words)
- Quote 2: "Check the .env file" (4 words)
- Source: User conversation on Jan 6, 2026
- Timestamp: 2026-01-06T14:35:00Z

Calculation:
- Quote count: 2 quotes = 4 pts
- Length: 13 words ÷ 12 = 1.08 pts
- Source: +1 pt
- Timestamp: +1 pt
Total: 7.08 points → MEDIUM confidence
```

### Example 3: Low Score
```
Evidence:
- Quote 1: "Try CODE-DELTA" (2 words)
- Source: User conversation on Jan 6, 2026
- Timestamp: 2026-01-06T14:40:00Z

Calculation:
- Quote count: 1 quote = 2 pts
- Length: 2 words ÷ 12 = 0.17 pts
- Source: +1 pt
- Timestamp: +1 pt
Total: 4.17 points → LOW confidence
```

## Threshold Guidelines

### Strict Interpretation
- **8-10**: HIGH → ACTIVE status
- **5-7**: MEDIUM → TENTATIVE or ACTIVE (judgment)
- **2-4**: LOW → TENTATIVE or wait
- **0-1**: None → Don't store

### Context Adjustments

**Raise threshold (+1 to +2 points needed):**
- Sensitive information (PII, medical, financial)
- Irreversible actions (deletions, deployments)
- Critical safety information

**Lower threshold (-1 to -2 points OK):**
- Technical details that are easily verifiable
- Preferences (low risk if wrong)
- Temporary information (can be updated)

**Quality over quantity:**
- One very detailed quote (20+ words) may be worth more than three short quotes
- Specific numbers/dates increase value even with lower word count
- Context richness matters (when/where/why provided)

## Edge Cases

### Case: One long detailed quote
```
Quote: "According to the documentation updated on March 15th, the retry mechanism uses exponential backoff starting at 100ms, doubling each time, with a maximum of 5 attempts before throwing a TimeoutException" (30 words)

Math score:
- 1 quote = 2 pts
- 30 words ÷ 12 = 2.5 pts
- Source + timestamp = 2 pts
Total: 6.5 points

Adjustment: Treat as 8+ because:
- Extremely detailed (date, mechanism, values, exception type)
- Specific technical information
- Would be hard to confabulate

Store as HIGH confidence despite math saying MEDIUM.
```

### Case: Multiple short but complete quotes
```
Quote 1: "Use port 8080" (3 words)
Quote 2: "Host is localhost" (3 words)
Quote 3: "SSL is disabled" (3 words)

Math score:
- 3 quotes = 6 pts
- 9 words ÷ 12 = 0.75 pts
- Source + timestamp = 2 pts
Total: 8.75 points → HIGH

Even though quotes are short, they're complete facts.
Math works out correctly.
```

### Case: Vague or uncertain evidence
```
Quote: "I think it was something like 30 or 60 seconds" (10 words)

Math score:
- 1 quote = 2 pts
- 10 words ÷ 12 = 0.83 pts
- Source + timestamp = 2 pts
Total: 4.83 points

Adjustment: Stay at LOW or don't store because:
- Uncertainty indicators ("I think", "something like", "or")
- Range not specific value
- Would need confirmation

Store as LOW confidence or wait for clarification.
```

## Frequency and Importance Modifiers

**Original tool routing rule:**
```
If evidence_score >= 8 AND (importance >= 5 OR frequency >= 2):
    ROUTE_ACTIVE
```

**Skill interpretation:**
- **Frequency**: How many times has this been mentioned?
  - First mention: frequency = 1
  - Repeated mention: frequency = 2
  - Multiple confirmations: frequency = 3
- **Importance**: How critical is this information? (1-10 scale)
  - Low: 1-3 (nice to know)
  - Medium: 4-7 (useful)
  - High: 8-10 (critical)

**Combined judgment:**
- High importance + low evidence → Store as MEDIUM (might need later)
- Low importance + high evidence → Store as HIGH (well-supported even if not critical)
- High importance + high evidence → Definitely ACTIVE
- Low importance + low evidence → Skip or LOW confidence

## When to Override the Formula

**Store despite low score:**
- Specific technical values (port numbers, timeouts, limits)
- User explicitly says "remember this"
- Critical safety information
- Legal/compliance requirements

**Don't store despite high score:**
- Sensitive information (PII) unless score >= 10
- Contradicts recent high-confidence fact
- User says "just thinking out loud" or similar
- Temporary/speculative information

**Ask for clarification despite any score:**
- Uncertainty indicators in quotes
- Contradicts existing memory
- Seems important but evidence is vague
- Technical details that could have multiple interpretations
