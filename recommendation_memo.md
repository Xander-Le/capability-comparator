# Wire Transfer Extraction: Model Recommendation

## Recommendation
I recommend **Claude Haiku** for production wire transfer field extraction based on its 
superior null handling, 2x faster latency, and 3.7x lower cost — with negligible accuracy 
trade-off compared to Claude Sonnet.

## What We Tested
We ran 10 fictional but realistic wire transfer instruction documents through two Claude 
models (Sonnet and Haiku) using an identical system prompt at temperature=0. Documents 
included standard instructions, written-out amounts, missing fields, a cross-border EUR 
transaction, and a deliberately incomplete instruction. Models were scored on field 
accuracy, null handling, format compliance, latency, and cost per document.

## Key Findings
- **Null handling**: Haiku scored 100% vs. Sonnet at 95% — Sonnet hallucinated a value 
  on one null field test. In a wire transfer context, a fabricated account number or 
  routing number could misdirect funds. This is the most safety-critical finding.
- **Field accuracy**: Sonnet scored 96.9% vs. Haiku at 95.6% — a 1.3% difference that 
  does not justify the cost and latency premium for this task.
- **Latency**: Haiku averaged 1.43s vs. Sonnet at 2.57s — important for real-time wire 
  processing workflows with SLA requirements.
- **Cost**: Haiku averages $0.0007 per document vs. Sonnet at $0.0026 — 3.7x cheaper. 
  At 10,000 documents/day that is ~$255/month vs. ~$950/month.
- **Format compliance**: Both models scored 100% — all responses parsed as valid JSON 
  after stripping Haiku's occasional markdown wrapper.

## Trade-offs
Haiku occasionally wraps JSON output in markdown code blocks despite explicit instructions 
not to. This requires a post-processing step in production. Sonnet follows formatting 
instructions more reliably — if downstream systems cannot tolerate any variance in output 
format, Sonnet is the safer choice.

## What We Would Test Next
- Rerun with real noisy wire instructions from an operations team to test robustness on 
  non-standard formatting
- Add a confidence score per field to flag low-certainty extractions for human review
- Test Claude Haiku with a tightened prompt to eliminate the markdown wrapping issue 
  and recheck format compliance