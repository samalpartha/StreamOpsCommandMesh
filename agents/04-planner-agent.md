# StreamOps Planner Agent

**Stage:** 4 of 8
**Version:** 2.00
**Model:** GPT-4o

## Purpose
Generates a structured, step-by-step remediation plan based on the risk-scored incident context. Assigns owners, priorities, and estimated completion times for each action.

## Input
Risk-scored incident context from Risk Agent, including risk level, HITL requirement, and enriched incident details.

## Output (JSON)
```json
{
  "plan_id": "PLAN-2026-0319-001",
  "incident_id": "INC-2026-0319-001",
  "risk_level": "HIGH",
  "steps": [
    {
      "step": 1,
      "action": "Rollback payments-api-service to v2.2.9",
      "owner": "platform-engineering",
      "priority": "P1",
      "estimated_minutes": 5,
      "type": "cicd_rollback"
    },
    {
      "step": 2,
      "action": "Page on-call engineer",
      "owner": "incident-commander",
      "priority": "P1",
      "estimated_minutes": 2,
      "type": "notification"
    },
    {
      "step": 3,
      "action": "Create Jira P1 ticket for incident tracking",
      "owner": "qa-automation",
      "priority": "P1",
      "estimated_minutes": 1,
      "type": "jira_ticket"
    },
    {
      "step": 4,
      "action": "Send Slack alert to #incidents-critical",
      "owner": "qa-automation",
      "priority": "P1",
      "estimated_minutes": 1,
      "type": "slack_alert"
    },
    {
      "step": 5,
      "action": "Run post-rollback smoke tests",
      "owner": "qa-engineering",
      "priority": "P1",
      "estimated_minutes": 10,
      "type": "verification"
    }
  ],
  "total_estimated_minutes": 19,
  "requires_approval": true
}
```

## Design Notes
- Plan steps are ordered by urgency — rollback and containment first, notification second, verification last
- Each step includes an `owner` field for accountability and a `type` field for downstream Action Agent routing
- `requires_approval: true` ensures the plan is held at the Approval Agent gate before execution
