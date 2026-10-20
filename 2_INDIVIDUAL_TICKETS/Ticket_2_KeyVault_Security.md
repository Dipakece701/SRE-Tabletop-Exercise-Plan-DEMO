PARENT STORY: AB#34970 (Link type: Child | Work item type: Task)
AREA PATH: Helios\QRE

======================================
TITLE:
=====================================
[INCIDENT SIMULATION] Key Vault Unauthorized Access Spike on helios-dm0-kv (Track B)

======================================
ASSIGNED TO:
=====================================
Dipak Singh

=====================================
TAGS:
=====================================
incident, sev-1, security, tabletop, soc2-cc7

=====================================
DESCRIPTION (Copy all lines below):
=====================================
Tabletop Incident Simulation — Track B (Security)
Fulfills SOC 2 Type II CC7.3 / CC7.4 security incident response evidence under parent work item AB#34970.

1. Incident Details:

- Impacted Resource: helios-dm0-kv (Azure Key Vault in helios-dm0-us-west3-rg)
- Trigger Alert: Microsoft Defender for Cloud — 18 unauthorized secret retrieval attempts (HTTP 403 Forbidden) within 5 minutes from untrusted external IP 198.51.100.44 targeting secret redis-cache-connection.
- Severity: Sev 1 (Critical Security / Credential Compromise)
- Notification Route: Action Group ag-helios-dm0-ops -> Slack #alerts-dm0-ops

2. Lifecycle Execution:

- Who Acked: Acknowledged by Dipak Singh in Slack at 10:27 UTC (within 2 minutes).
- Joint Escalation: Immediately escalated to Constantin Pricochi and Maurice Kennedy per security escalation protocol.
- Containment Actions:

  1. Maurice Kennedy investigated Microsoft Entra ID and immediately revoked/deleted the compromised Service Principal credential.
  2. Dipak Singh updated Key Vault network firewall rules to enforce trusted Azure VNets and private endpoints only.
  3. Dipak Singh rotated the redis-cache-connection secret and restarted dependent workloads with the updated secret version.

3. Recovery Verification (4-Point Technical Checklist):
[x] Audit Log Zero: KQL query in helios-dm0-logs confirmed 0 requests from offending IP 198.51.100.44 post-containment over 30 minutes.
[x] Workload Health: Backend workloads (ontology-event-processor, cost-ingestion) verified authenticating and retrieving new secret via Workload Identity with HTTP 200 OK.
[x] Defender Incident Status: Marked Mitigated — Credential Rotated & Network Isolated.
[x] Security Sign-off: Constantin Pricochi reviewed audit log verification and signed off on incident closure.
