# StreamOps Risk Agent

**Stage:** 3 of 8
**Version:** 2.00
**Model:** GPT-4o

## Purpose
Evaluates the enriched incident context and produces a quantified risk score. Determines whether human-in-the-loop approval is required before remediation actions can proceed.

## Input
Enriched incident context from Analyst Agent, including root cause hypothesis, SLA breach risk, and historical patterns.

## Output (JSON)
```json
{
  "risk_level": "HIGH",
  "confidence": 0.94,
  "HITL_required": true,
  "rationale": "Active revenue loss at $12,400/min with 18.7% error rate on payment-critical endpoints",
  "risk_factors": [
    "Production environment affected",
    "Revenue-generating endpoints impacted",
    "Error rate exceeds 10% threshold",
    "SLA breach imminent"
  ]
}
```

## Risk Scoring Matrix

| Risk Level | Criteria | HITL Required |
|---|---|---|
| LOW | Error rate <5%, no revenue impact, non-prod | No |
| MEDIUM | Error rate 5-10%, minor revenue impact | No |
| HIGH | Error rate >10%, active revenue loss | Yes |
| CRITICAL | Full outage, major revenue loss, data integrity risk | Yes |

## Design Notes
- Confidence score reflects model certainty in the risk assessment (0.0–1.0)
- `HITL_required: true` gates the pipeline at Stage 5 — no actions execute without human approval
- Risk factors array provides explainability for the risk decision
