# StreamOps Document Agent

**Stage:** 7 of 8
**Version:** 2.00
**Model:** GPT-4o

## Purpose
Generates a structured incident report from the full pipeline execution context. Produces compliance-ready documentation including timeline, actions taken, outcomes, and recommendations.

## Input
Execution results from Action Agent, including all actions performed, their statuses, and the full incident context accumulated through the pipeline.

## Output (JSON)
```json
{
  "report_id": "RPT-2026-0319-001",
  "incident_id": "INC-2026-0319-001",
  "title": "Incident Report — payments-api-service CI/CD Failure",
  "generated_at": "2026-03-19T09:15:00Z",
  "summary": "CI/CD pipeline failure in payments-api-service production deployment v2.3.1. 47 of 312 API contract tests failed affecting /v2/subscribe, /v2/renew, /v2/cancel. Error rate spiked from 0.3% to 18.7%. Revenue impact estimated at $12,400/minute.",
  "timeline": [
    {"time": "08:42:00Z", "event": "CI/CD failure detected"},
    {"time": "08:43:00Z", "event": "Incident classified as P1 by Intake Agent"},
    {"time": "08:45:00Z", "event": "Root cause identified: API contract regression in v2.3.1"},
    {"time": "08:47:00Z", "event": "Risk assessed as HIGH, HITL approval required"},
    {"time": "08:50:00Z", "event": "Remediation plan generated"},
    {"time": "08:58:00Z", "event": "Plan approved by Partha Samal"},
    {"time": "09:00:00Z", "event": "Jira INFRA-4521 created, Slack alert sent"},
    {"time": "09:02:00Z", "event": "Rollback to v2.2.9 initiated"},
    {"time": "09:10:00Z", "event": "Service restored, error rate returned to 0.3%"}
  ],
  "actions_taken": [
    "Jira P1 ticket INFRA-4521 created",
    "Slack alert sent to #incidents-critical",
    "CI/CD rollback: v2.3.1 → v2.2.9"
  ],
  "outcome": "Service restored. Error rate returned to baseline (0.3%). No data loss.",
  "mttr_minutes": 28,
  "recommendations": [
    "Add contract test coverage for payment endpoint edge cases",
    "Implement canary deployment for payments-api",
    "Add pre-deploy contract validation gate to CI/CD pipeline"
  ]
}
```

## Design Notes
- Called as a nested agent by the Action Agent after execution completes
- Report format is designed to be compliance-ready for SOC2/ISO audit requirements
- Timeline is reconstructed from pipeline stage timestamps
- MTTR (Mean Time To Resolve) is calculated from first detection to service restoration
- Recommendations are generated based on root cause analysis from the Analyst Agent
