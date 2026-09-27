# LLM Evaluation Lab

## Evaluating LLM-as-a-Judge Reliability for Customer Support

This project evaluates whether an efficiency-oriented language model can reliably
perform criterion-level customer-support quality evaluation against an established
benchmark.

Using the Sprinklr CXM Arena `Agent_Quality_Adherence` dataset, I built a Python
evaluation pipeline to test GPT-5.6 Luna as an LLM judge across a balanced sample
of 100 unique customer-support conversations.

The judge achieved **84% agreement** with the CXM Arena reference labels. Manual
review of the 16 disagreements showed that the primary reliability challenge was
not simply conversation comprehension, but how the model interpreted evaluation
rubrics, evidence requirements, and criterion boundaries.

## Key Results

- **100** unique customer-support conversations evaluated
- **84%** agreement with CXM Arena reference labels
- **84% precision, recall, and F1** for both `Yes` and `No` cases
- **8 false positives** and **8 false negatives**
- **16 disagreements** manually reviewed and categorized
- **12 of 16 disagreements (75%)** concentrated in three recurring patterns:
  - scope interpretation mismatch
  - criterion threshold mismatch
  - evidence standard mismatch
- **153,883 total tokens** used across the evaluation
- Approximately **$0.043 estimated inference cost** using standard uncached-input pricing at the time of the experiment

## Research Question

Can an efficiency-oriented language model reliably perform criterion-level
customer-support quality evaluation against an established benchmark while
remaining operationally cost-effective?

The experiment examined:

1. Agreement between model judgments and CXM Arena reference labels
2. Performance across `Yes` and `No` evaluation cases
3. Recurring failure patterns when the model and benchmark disagreed
4. Operational implications of reliability and inference cost

## Methodology

The source dataset contained nested evaluation records associated with repeated
customer-support conversations. The data was transformed to create one row per
unique conversation and evaluation-criterion pair.

To reduce repeated-conversation contamination, one evaluation criterion was
randomly selected from each unique conversation. A reproducible balanced sample
of 100 cases was then created with 50 `Yes` and 50 `No` reference labels.

During inference, the LLM judge received only:

- the complete customer-agent conversation
- the evaluation criterion

Reference labels, reference explanations, proof message IDs, and confidence
scores were withheld until inference was complete.

All 100 cases were evaluated using the same prompt and model configuration.
The prompt was not modified in response to individual test-set results.

## Error Analysis

Overall agreement did not fully explain judge reliability.

The 16 disagreements were manually reviewed using a structured annotation
workflow. Seven qualitative failure categories were identified.

The three dominant patterns accounted for 75% of all disagreements:

- **Scope interpretation mismatch (5 cases):** The judge applied the criterion
  more broadly than the reference label.
- **Criterion threshold mismatch (4 cases):** The judge and reference label
  applied different standards for how much evidence was sufficient.
- **Evidence standard mismatch (3 cases):** The judge required more explicit
  evidence than the reference label.

Scope interpretation accounted for five of eight false positives, while evidence
standard mismatches appeared only among false negatives in this sample.

These results suggest that LLM judge reliability depends not only on model
capability, but also on rubric construction and the definition of acceptable
evidence.

## Deployment Implications

The tested configuration may be useful as an assistive layer for high-volume
customer-support QA, but the observed disagreement patterns do not support
treating the judge as a fully autonomous replacement for human quality review.

A stronger production implementation would include:

- explicit rubric boundaries
- atomic rather than compound evaluation criteria
- defined evidence standards
- clear rules for conditional criteria
- structured output enforcement and validation
- human review for ambiguous or consequential judgments
- additional validation using organization-specific data

## Repository Structure

```text
llm-evaluation-lab/
├── data/
│   └── evaluation_sample.jsonl
├── notebooks/
│   └── evaluation_analysis.ipynb
├── results/
│   ├── error_analysis.csv
│   └── evaluation_results.jsonl
├── README.md
└── requirements.txt
```
