# AI Capability Comparator: Wire Transfer Extraction

**A model-selection decision, run like a product decision.** This project benchmarks two Claude models (Sonnet and Haiku) on a real commercial-banking task — extracting structured fields from unstructured wire transfer instructions — and turns the results into a costed, defensible production recommendation.

> **What this demonstrates as an AI PM:** framing an ambiguous "which model should we ship?" question as a measurable evaluation, defining the right success metrics for a regulated workflow, and making a build decision that trades accuracy, latency, and cost against real risk.

## The problem

Wire transfer processing is a core, high-stakes commercial banking operation. Instructions arrive as free text — emails, faxes, message fields — and someone (or something) has to pull out the beneficiary name, account number, routing/BIC, amount, currency, and reference fields before the payment can be processed. Doing this by hand is slow and error-prone; a single wrong digit moves money to the wrong place.

It's a strong candidate for LLM automation, but "use AI" isn't a decision. The real questions a PM owns are: *Which* model? At what accuracy? At what failure mode? At what cost per document at the volume we actually run? This project answers those.

## Why this task is the right test

Field extraction from wire instructions has clear right/wrong answers, which makes it possible to score models rigorously instead of relying on vibes. It also surfaces the failure mode that matters most in banking: a model that **invents** a missing routing number is far more dangerous than one that admits the field is absent. That distinction drives how I chose the evaluation metrics below.

## What I measured (and why each metric matters)

| Metric | What it captures | Why a PM cares |
| --- | --- | --- |
| **Field accuracy** | Did the model extract the correct values? | Baseline quality bar |
| **Null handling** | Did it return `null` instead of hallucinating a missing field? | The critical safety metric — hallucinated payment fields are a compliance and loss event |
| **Format compliance** | Valid JSON on every call? | Determines integration reliability into downstream payment systems |
| **Latency** | Avg response time per document | Throughput / SLA at processing volume |
| **Cost** | Price per document at each model tier | Unit economics at production scale |

## Key findings

- **Haiku scored 100% on null handling vs. Sonnet's 95%** — the cheaper model was actually *safer* on the most critical banking test.
- **Sonnet led field accuracy 96.9% to 95.6%** — a 1.3-point gap.
- **Haiku was 2× faster** (1.43s vs 2.57s) and **3.7× cheaper** ($0.0007 vs $0.0026 per document).
- Both hit **100% format compliance** after post-processing.

## Recommendation

**Ship Claude Haiku for production wire transfer extraction.** It wins on the metric that carries the most risk (null handling), runs faster, and costs ~73% less per document, at a negligible accuracy trade-off that can be closed with validation rules. The full reasoning, including where the residual accuracy gap should be backstopped with deterministic checks, is in the [recommendation memo](https://github.com/Xander-Le/capability-comparator/blob/main/recommendation_memo.md).

This is the heart of the PM artifact: not "Sonnet is smarter," but "here is the model that meets the risk, latency, and cost requirements of *this* workflow, and here is the evidence."

## Tradeoffs and what I'd do next

- **Accuracy vs. safety:** I prioritized null handling over raw accuracy because in payments, a confidently wrong field is more expensive than a flagged-for-review one. A different workflow (e.g. low-stakes enrichment) would weight these differently — and the framework re-runs cleanly against new weights.
- **Next iteration:** expand the test set across instruction formats (SWIFT MT103, domestic Fedwire, free-text email), add a human-in-the-loop confidence threshold, and measure escalation rate as a sixth metric.

## How to run

1. Clone the repo
2. `pip install -r requirements.txt`
3. Add your API keys — create a `.env` file with your `ANTHROPIC_API_KEY`
4. Launch JupyterLab
5. Open `capability_comparator.ipynb` and run all cells in order

## Stack

Python · Jupyter · Anthropic Claude API (Sonnet & Haiku) · pandas · matplotlib

---

*Part of a portfolio demonstrating AI product management in banking and payments: capability evaluation (this project) → [RAG over bank filings](https://github.com/Xander-Le/rag-wab-filings) → [tool-calling payments agent](https://github.com/Xander-Le/payment-rail-agent).*
