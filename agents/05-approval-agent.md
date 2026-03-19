# StreamOps Approval Agent

**Stage:** 5 of 8
**Version:** 2.00
**Model:** GPT-4o

## Purpose
Human-in-the-loop (HITL) approval gate. Presents the remediation plan to a human reviewer for approval or rejection before any system actions are executed. This is the critical governance checkpoint in the pipeline.

## Input
Structured remediation plan from Planner Agent, including risk level, proposed actions, and estimated impact.

## Output (JSON)
```json
{
  "approval_id": "APPR-2026-0319-001",
  "incident_id": "INC-2026-0319-001",
  "plan_id": "PLAN-2026-0319-001",
  "decision": "APPROVED",
  "approver": "Partha Samal",
  "approver_role": "QA Lead",
  "timestamp": "2026-03-19T08:58:00Z",
  "conditions": [],
  "notes": "Approved — rollback to v2.2.9 is the correct remediation path"
}
```

## Decision States

| Decision | Effect |
|---|---|
| APPROVED | Pipeline proceeds to Action Agent (Stage 6) |
| REJECTED | Pipeline halts, incident returned to Planner for revised plan |
| APPROVED_WITH_CONDITIONS | Pipeline proceeds with modified scope per approver conditions |

## Design Notes
- This agent does NOT auto-approve — it requires explicit human input
- Approval is mandatory when `HITL_required: true` is set by the Risk Agent (HIGH or CRITICAL risk)
- LOW and MEDIUM risk incidents may bypass this gate via auto-approval policy
- All approval decisions are logged immutably by the Audit Agent (Stage 8)
- Supports conditional approval where the approver can modify scope before proceeding
