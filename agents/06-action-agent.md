# StreamOps Action Agent

**Stage:** 6 of 8
**Version:** 3.00
**Model:** GPT-4o
**Live:** [Try on Airia Catalog](https://airia.ai/catalog?assistantId=add10598-2169-4adb-b79d-9ffdee0704ff)

## Purpose
Executes the approved remediation plan by performing system actions: creating Jira tickets, sending Slack/Teams alerts, and triggering CI/CD rollbacks. Produces both machine-readable JSON and human-readable notification output.

## Input
Approved remediation plan from Approval Agent, including the list of actions to execute with parameters.

## Output (JSON)
```json
{
  "execution_id": "EXEC-2026-0319-001",
  "incident_id": "INC-2026-0319-001",
  "approval_id": "APPR-2026-0319-001",
  "actions_executed": [
    {
      "action": "jira_ticket_created",
      "ticket_id": "INFRA-4521",
      "project": "INFRA",
      "priority": "P1",
      "status": "SUCCESS",
      "response_code": 200
    },
    {
      "action": "slack_alert_sent",
      "channel": "#incidents-critical",
      "message": "🔴 P1 INCIDENT: payments-api deploy failed. 18.7% error rate. Rollback initiated. Jira: INFRA-4521",
      "status": "SUCCESS",
      "response_code": 200
    },
    {
      "action": "cicd_rollback_triggered",
      "service": "payments-api-service",
      "from_version": "v2.3.1",
      "to_version": "v2.2.9",
      "status": "SUCCESS",
      "response_code": 200
    }
  ],
  "all_actions_successful": true
}
```

## Nested Agent Calls
After execution completes, the Action Agent triggers:
1. **Document Agent** (Stage 7) — passes execution results for incident report generation
2. **Audit Agent** (Stage 8) — passes full pipeline context for audit log creation

## Interfaces
- Chat (Airia Catalog — public)
- API endpoint (Airia Gateway)

## Design Notes
- Dual output: structured JSON for automation + natural language for Slack/Teams human readability
- All integrations (Jira, Slack, CI/CD) are mocked in demo; production would use real API calls
- Each action reports individual `status` and `response_code` for granular failure tracking
- If any action fails, remaining actions still execute (no short-circuit) and failures are flagged
