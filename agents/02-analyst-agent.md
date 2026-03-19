# StreamOps Analyst Agent

**Stage:** 2 of 8
**Version:** 2.00
**Model:** GPT-4o
**Live:** [Try on Airia Catalog](https://airia.ai/catalog?assistantId=45441d25-a328-454e-88b0-de6042259a14)

## Purpose
Enriches the incident context with historical failure patterns, SLA data, and root cause hypotheses. Feeds the enriched context to the Risk Agent.

## Input
Classified incident JSON from Intake Agent.

## Output (JSON)
```json
{
  "root_cause_hypothesis": "API contract regression introduced in v2.3.1 deploy",
  "historical_pattern": "3rd contract failure on payments-api in 90 days",
  "sla_breach_risk": true,
  "mttr_estimate_minutes": 25,
  "recommended_priority": "P1"
}
```

## Design Notes
- Enriches with mock SLA/historical data in demo; production would query Jira/Confluence
- Produces root cause hypothesis that feeds directly into Planner Agent's step generation
