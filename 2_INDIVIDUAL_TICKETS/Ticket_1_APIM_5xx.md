PARENT STORY: AB#34970 (Link type: Child | Work item type: Task)
AREA PATH: Helios\QRE

================================================================================
TITLE:
================================================================================
[INCIDENT SIMULATION] APIM Gateway 5xx Failure Rate Surge on helios-dm0-apim (Track A)

================================================================================
ASSIGNED TO:
================================================================================
Dipak Singh

================================================================================
TAGS:
================================================================================
incident, sev-2, tabletop, soc2-cc7, reliability

================================================================================
DESCRIPTION (Copy all lines below):
================================================================================
Tabletop Incident Simulation — Track A (Reliability)
Fulfills SOC 2 Type II CC7.1 / CC7.2 incident response evidence under parent work item AB#34970.

1. Incident Details:

- Impacted Resource: helios-dm0-apim (Resource Group: helios-dm0-us-west3-rg)
- Trigger Alert: helios-dm0-gateway-failure-rate (42% failure rate observed over 15m window, threshold > 10%)
- Severity: Sev 2 (Major Service Degradation)
- Notification Route: Action Group ag-helios-dm0-ops -> Slack #alerts-dm0-ops

2. Lifecycle Execution:

- Who Acked: Acknowledged by Dipak Singh in Slack at 10:20 UTC (within 2 minutes of detection). Escalation backup: Maurice Kennedy.
- Incident Commander: Constantin Pricochi
- Diagnostic Investigation: Aditya Mehta executed KQL query in Log Analytics (helios-dm0-logs) isolating errors to upstream HTTP 504 gateway timeouts on container ontology-event-processor.
- Mitigation: Rollback authorized by Constantin Pricochi; Maurice Kennedy executed container rollback in GitOps.

3. Recovery Verification (4-Point Technical Checklist):
[x] Metric Baseline: APIM 5xx error rate dropped from 42% to 0.08% and remained stable for > 15 consecutive minutes.
[x] Synthetic Health Probe: GET /ontology-event-processor/health returned HTTP 200 OK in 48ms.
[x] Diagnostic Log Audit: Log Analytics confirmed 0 gateway 504 errors in subsequent 10-minute window.
[x] Sign-off: Dipak Singh confirmed recovery evidence in Slack and resolved incident record.
