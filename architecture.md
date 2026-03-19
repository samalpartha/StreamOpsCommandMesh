# StreamOps CommandMesh — Architecture

## Pipeline Overview

```mermaid
flowchart TD
    trigger["🔔 Incident Trigger<br/><i>Jira · CI/CD · API Monitor</i>"]

    intake["1 · Intake Agent<br/>Receives raw alert<br/>Classifies type, severity, affected system"]
    analyst["2 · Analyst Agent<br/>Enriches with historical patterns, SLA data<br/>Produces root cause hypothesis"]
    risk["3 · Risk Agent<br/>Scores risk: LOW / MEDIUM / HIGH / CRITICAL<br/>Sets HITL_required flag if HIGH or CRITICAL"]
    planner["4 · Planner Agent<br/>Generates step-by-step remediation plan<br/>Assigns owners, priorities, estimated times"]
    approval{{"5 · Approval Agent — HITL Gate<br/>⚠️ Human reviews plan<br/>APPROVE / REJECT"}}
    action["6 · Action Agent<br/>Creates Jira ticket<br/>Sends Slack/Teams alert<br/>Triggers CI/CD rollback"]
    document["7 · Document Agent<br/>Generates structured incident report<br/>Compliance-ready: timeline, actions, outcomes"]
    audit["8 · Audit Agent<br/>Produces immutable audit log<br/>10-event trail, COMPLIANT status, signature_hash"]

    trigger --> intake
    intake --> analyst
    analyst --> risk
    risk --> planner
    planner --> approval
    approval -- "✅ APPROVED" --> action
    approval -. "❌ REJECTED" .-> planner
    action --> document
    action --> audit

    style trigger fill:#1e3a5f,stroke:#4a90d9,color:#ffffff,stroke-width:2px
    style intake fill:#1a5276,stroke:#5dade2,color:#ffffff,stroke-width:2px
    style analyst fill:#1a5276,stroke:#5dade2,color:#ffffff,stroke-width:2px
    style risk fill:#7b241c,stroke:#e74c3c,color:#ffffff,stroke-width:2px
    style planner fill:#1a5276,stroke:#5dade2,color:#ffffff,stroke-width:2px
    style approval fill:#7d6608,stroke:#f1c40f,color:#ffffff,stroke-width:3px
    style action fill:#0e6655,stroke:#1abc9c,color:#ffffff,stroke-width:2px
    style document fill:#1a5276,stroke:#5dade2,color:#ffffff,stroke-width:2px
    style audit fill:#4a235a,stroke:#af7ac5,color:#ffffff,stroke-width:2px
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
