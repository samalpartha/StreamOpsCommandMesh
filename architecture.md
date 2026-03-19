# StreamOps CommandMesh — Architecture

## Pipeline Overview

```
[Incident Trigger]
       │
       ▼
┌─────────────────┐
│  1. Intake Agent │  ← Receives raw alert (Jira, CI/CD, API monitor)
│                 │     Classifies type, severity, affected system
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 2. Analyst Agent│  ← Enriches with historical patterns, SLA data
│                 │     Produces root cause hypothesis
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  3. Risk Agent  │  ← Scores risk: LOW / MEDIUM / HIGH / CRITICAL
│                 │     Sets HITL_required flag if HIGH or CRITICAL
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 4. Planner Agent│  ← Generates step-by-step remediation plan
│                 │     Assigns owners, priorities, estimated times
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 5. Approval Agent│ ← HUMAN-IN-THE-LOOP gate
│   (HITL)        │    Human reviews plan and approves or rejects
└────────┬────────┘
         │ [APPROVED]
         ▼
┌─────────────────┐
│ 6. Action Agent │  ← Executes approved actions:
│                 │     • Creates Jira ticket
│                 │     • Sends Slack/Teams alert
│                 │     • Triggers CI/CD rollback
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│7. Document Agent│  ← Generates structured incident report
│                 │     Compliance-ready: timeline, actions, outcomes
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  8. Audit Agent │  ← Produces immutable audit log
│                 │     10-event trail, COMPLIANT status, signature_hash
└─────────────────┘
```

## Design Decisions

### Why 8 agents instead of 1?
Each agent has a single, well-defined responsibility. This enables:
- Independent testing of each stage
- Swappable models per stage (cheaper model for classification, GPT-4o for planning)
- Clear audit trail of which agent made which decision

### Why HITL at Stage 5?
Risk-scored incidents above HIGH require human sign-off before any real system actions (Jira, Slack, rollback). This prevents false-alarm fatigue and ensures accountability.

### Why dual JSON + natural language output?
The Action Agent produces both a machine-readable JSON payload (for downstream automation) and a human-readable Slack/Teams message. This enables both automated routing and immediate human comprehension.

### Nested Agent Architecture
The Action Agent calls the Document Agent and Audit Agent as nested agents, passing execution results downstream. This keeps orchestration clean and each agent stateless.

## Platform
Built entirely on Airia Agent Studio — no custom code required. All orchestration, model calls, prompt injection, and nested agent routing handled by the Airia runtime.
