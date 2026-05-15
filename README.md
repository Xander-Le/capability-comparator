# AI Capability Comparator: Wire Transfer Extraction

Compares two Claude models (Sonnet and Haiku) on a real banking task: extracting 
structured fields from wire transfer instructions.

## Why this task
Wire transfer processing is a core commercial banking operation. Extracting structured 
data from unstructured wire instructions is a real AI use case at banks — and it has 
clear right/wrong answers, making it ideal for a rigorous model comparison.

## What I measured
- Field accuracy (did the model extract the correct values?)
- Null handling (did the model return null instead of hallucinating missing fields?)
- Format compliance (did the model return valid JSON every time?)
- Latency (average response time per document)
- Cost (per document at each model tier)

## Key findings
- Haiku scored 100% on null handling vs. Sonnet at 95% — the cheaper model was safer 
  on the most critical banking test
- Sonnet scored 96.9% field accuracy vs. Haiku at 95.6% — a 1.3% difference
- Haiku is 2x faster (1.43s vs 2.57s) and 3.7x cheaper ($0.0007 vs $0.0026 per doc)
- Both models achieved 100% format compliance after post-processing

## Recommendation
Claude Haiku for production wire transfer extraction — superior null handling, faster 
latency, and significantly lower cost with negligible accuracy trade-off.

[Full analysis](recommendation_memo.md)

## How to run
