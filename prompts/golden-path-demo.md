# Golden Path Demo — CI/CD Failure Scenario

This is the exact test scenario run during the hackathon demo to validate the full 8-agent pipeline end-to-end.

## Scenario

> CI/CD failure in the payments API on a major streaming platform. Risk is HIGH. Human approval required. Jira ticket to be created, Slack alert sent, rollback executed, incident report generated, audit log produced.

***

## Stage 1 — Intake Agent Input

```
INCIDENT ALERT — CI/CD Pipeline Failure

System: payments-api-service
Environment: production
Pipeline: github-actions/deploy-payments-v2.3.1
Failure stage: integration-tests
Error: 47 of 312 API contract tests failed
Affected endpoints: /v2/subscribe, /v2/renew, /v2/cancel
Error rate spike: 0.3% → 18.7% in last 15 minutes
Revenue impact: estimated $12,400/minute
On-call engineer: not yet paged
Time of failure: 2026-03-19T08:42:00Z
```

**Expected Output:** Incident classified as CI/CD_FAILURE, severity P1, routed to Risk Agent.

***

## Stage 3 — Risk Agent Input

```
Assess risk for the following incident:
- Type: CI/CD Pipeline Failure
- System: payments-api-service (production)
- Error rate: 18.7% on payment endpoints
- Revenue impact: $12,400/minute
- Duration: 15 minutes active
- Customer impact: subscription renewals failing
Provide risk_level (LOW/MEDIUM/HIGH/CRITICAL), confidence score, and HITL_required boolean.
```

**Expected Output:**
```json
{
  "risk_level": "HIGH",
  "confidence": 0.94,
  "HITL_required": true,
  "rationale": "Active revenue loss at $12,400/min with 18.7% error rate on payment-critical endpoints"
}
```

***

## Stage 4 — Planner Agent Input

```
Generate a remediation plan for:
- Incident: CI/CD failure, payments-api-service, production
- Risk: HIGH, HITL required
- Failed tests: 47/312 API contract tests on /v2/subscribe, /v2/renew, /v2/cancel
- Revenue impact: $12,400/minute

Produce a structured JSON plan with ordered steps, owners, priorities.
```

**Expected Output:** JSON plan with steps: rollback deploy, page on-call, create Jira P1, send Slack alert, run smoke tests.

***

## Stage 5 — Approval Agent Input

```
APPROVAL REQUEST — Human-in-the-Loop Gate

Incident: payments-api-service CI/CD failure, production
Risk: HIGH | Revenue loss: $12,400/min
Proposed actions:
  1. Roll back payments-api to v2.2.9
  2. Create Jira P1 ticket
  3. Send Slack alert to #incidents-critical

Decision: APPROVED
Approver: Partha Samal
Timestamp: 2026-03-19T08:58:00Z
```

***

## Stage 6 — Action Agent Input

```
Execute the following approved remediation actions:
1. Create Jira P1 ticket: "payments-api CI/CD failure - 47 contract tests failed in production"
   Project: INFRA, Priority: P1, Assignee: on-call-engineer
2. Send Slack alert to #incidents-critical: "🔴 P1 INCIDENT: payments-api deploy failed. 18.7% error rate. Rollback initiated. Jira: INFRA-4521"
3. Trigger CI/CD rollback: payments-api-service → v2.2.9

Report each action with status and response_code.
```

**Expected Output:**
```json
{
  "actions_executed": [
    {"action": "jira_ticket_created", "ticket_id": "INFRA-4521", "status": "SUCCESS", "response_code": 200},
    {"action": "slack_alert_sent", "channel": "#incidents-critical", "status": "SUCCESS", "response_code": 200},
    {"action": "cicd_rollback_triggered", "version": "v2.2.9", "status": "SUCCESS", "response_code": 200}
  ]
}
```

***

## Stage 8 — Audit Agent Input

```
Generate a complete audit log for the following incident lifecycle:
- Incident: payments-api-service CI/CD failure, 2026-03-19
- Pipeline stages completed: Intake, Analyst, Risk, Planner, Approval (HITL), Action, Document, Audit
- Approver: Partha Samal
- Actions taken: Jira INFRA-4521 created, Slack alert sent, rollback to v2.2.9
- Outcome: Service restored, error rate returned to 0.3%

Produce 10-event audit trail with timestamps, compliance_status, and signature_hash.
```

**Expected Output:** 10-event audit trail with `compliance_status: COMPLIANT` and `signature_hash`.
