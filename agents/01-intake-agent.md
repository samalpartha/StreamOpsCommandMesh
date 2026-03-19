# StreamOps Intake Agent

**Stage:** 1 of 8
**Version:** 3.00
**Model:** GPT-4o
**Live:** [Try on Airia Catalog](https://airia.ai/catalog?assistantId=dc954d79-a4ea-45f8-a60e-03583e4267b9)

## Purpose
Monitors incoming QA incident signals (Jira tickets, API test failures, CI/CD alerts). Classifies the incident type, severity, and affected system, then routes it to the appropriate downstream agent.

## Input
Raw incident alert text — from CI/CD pipeline output, monitoring tool, or manual report.

## Output (JSON)
```json
{
  "incident_id": "INC-2026-0319-001",
  "type": "CI_CD_FAILURE",
  "severity": "P1",
  "affected_system": "payments-api-service",
  "environment": "production",
  "routed_to": "AnalystAgent",
  "timestamp": "2026-03-19T08:42:00Z"
}
```

## Interfaces
- Chat (Airia Catalog — public)
- API endpoint (Airia Gateway)

## Design Notes
- Uses keyword classification to detect incident type (CI/CD, API degradation, test failure, outage)
- Sets severity based on error rate thresholds: >10% = P1, 5-10% = P2, <5% = P3
- Always outputs structured JSON regardless of input format quality
