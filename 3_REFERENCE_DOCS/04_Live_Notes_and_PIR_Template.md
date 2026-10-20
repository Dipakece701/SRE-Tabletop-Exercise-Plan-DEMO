# 04 — Incident Tabletop Retrospective & Live Notes

> **Instructions for Dipak:**  
> Keep this document open on your screen during the call on Thursday. Share your screen so the team can see you filling in the details live as they discuss!

---

# SRE Tabletop Exercise: Dual-Incident Simulation (SOC 2 CC7)

* **Exercise Date:** Thursday, October 22, 2026
* **Exercise Window:** 10:15 AM – 11:15 AM (60 minutes)
* **Governing SOP:** `00_Incident_Procedure_v1.md`
* **Target Environment:** DEMO (`dm0`)
* **Work Item Reference:** AB#34970 (SOC 2 CC7 Audit Evidence)

---

## 1. Attendees & Tabletop Roles

| Tabletop Role | Person | Contact / Escalation |
|---|---|---|
| **Incident Commander (IC)** | Constantin Pricochi | Slack `@Constantin Pricochi` |
| **Primary Responder** | Dipak Singh | Slack `@Dipak Singh` |
| **Backup Responder** | Maurice Kennedy | Slack `@Maurice Kennedy` |
| **Technical Investigator** | Aditya Mehta | Slack `@Aditya Mehta` |
| **Security Escalation Lead** | Constantin Pricochi | Slack `#security-ops` |

---

## 2. Track A: Reliability Incident — `helios-dm0-apim` 5xx Surge

* **Incident ID:** `INC-20261022-DM0-APIM`
* **Incident Record (ADO):** **AB#34974** (`[INCIDENT - Sev 2] APIM Gateway 5xx Spike on helios-dm0-apim`)
* **Severity:** Sev 2 (Major Service Degradation)
* **Who Acked:** **Dipak Singh** (Primary Responder) at **10:20 UTC** via Slack 👀 reaction + channel acknowledgment post in `#alerts-dm0-ops`.

### Timeline & Investigation:
* **10:18 UTC** — Alert `helios-dm0-gateway-failure-rate` fires (5xx failure rate = 42% over 15m window).
* **10:20 UTC** — Dipak acks. Escalates to IC Constantin. Severity set to Sev 2.
* **10:22 UTC** — Incident record AB#34974 created in ADO under `Helios\QRE`. Dedicated Slack bridge opened.
* **10:32 UTC** — Aditya queries `AzureDiagnostics` in `helios-dm0-logs`. Isolates issue to `ontology-event-processor` backend HTTP 504 timeouts.
* **10:45 UTC** — Constantin authorizes backend container restart / rollback.
* **10:47 UTC** — Maurice executes rollback to previous known-good deployment digest.

### How We Checked Recovery:
- [x] **Metric Baseline:** APIM 5xx error rate dropped from 42% to 0.08% and held stable for > 15 minutes.
- [x] **Synthetic Health Check:** `GET /ontology-event-processor/health` returned HTTP 200 in 48ms.
- [x] **Diagnostic Log Audit:** Log Analytics confirmed zero 504 errors in subsequent 10-minute window.
- [x] **Responder Sign-off:** Dipak confirmed recovery in Slack; AB#34974 marked Resolved.

---

## 3. Track B: Security Incident — `helios-dm0-kv` Unauthorized Access Spike

* **Incident ID:** `INC-20261022-DM0-KV-SEC`
* **Incident Record (ADO):** **AB#34978** (`[INCIDENT - Sev 1 Security] Anomalous Key Vault access attempts on helios-dm0-kv`)
* **Severity:** Sev 1 (High Security / Credential Threat)
* **Who Acked:** **Dipak Singh** (Primary Responder) at **10:27 UTC**; immediately paged **Constantin Pricochi** and **Maurice Kennedy**.

### Timeline & Containment:
* **10:25 UTC** — Microsoft Defender for Cloud alerts on 18 unauthorized secret retrieval attempts (HTTP 403) from untrusted external IP `198.51.100.44` on `helios-dm0-kv`.
* **10:27 UTC** — Dipak acks and escalates. Security incident bridge activated.
* **10:29 UTC** — Incident record AB#34978 created in ADO tagged `security`, `sev-1`.
* **10:35 UTC** — Audit analysis in `AzureDiagnostics` reveals an expired / leaked Service Principal credential was attempted against `redis-cache-connection`.
* **10:42 UTC** — Containment executed:
  1. Maurice revokes and deletes the compromised Service Principal credential in Microsoft Entra ID.
  2. Key Vault firewall updated to restrict public network access.
  3. Dipak rotates the `redis-cache-connection` secret in Key Vault and restarts dependent workloads.

### How We Checked Recovery:
- [x] **Audit Log Zero:** KQL query on `AzureDiagnostics | where ResourceProvider == "MICROSOFT.KEYVAULT" | where CallerIPAddress == "198.51.100.44"` confirms 0 requests in the last 30 minutes.
- [x] **Workload Health Check:** Legitimate services (`ontology-event-processor`, `cost-ingestion`) successfully pulled new secret via Managed Identity / Workload Identity.
- [x] **Defender Incident Status:** Defender for Cloud incident marked *Mitigated - Credential Rotated & Network Isolated*.
- [x] **Sign-off:** Constantin Pricochi signed off on containment and closure at 10:55 UTC.

---

## 4. Retrospective: What Went Well

1. **Adherence to Incident Procedure v1.0**: Clear distinction between Primary Responder, Backup Responder, and Escalation Lead prevented role confusion.
2. **Rapid Ack & Record Creation**: Both incidents were acknowledged within 2 minutes of detection, and formal ADO incident records were created within 5 minutes.
3. **Multi-Faceted Recovery Verification**: Both reliability and security tracks verified recovery using metrics, synthetic probes, and KQL audit queries rather than guessing.

---

## 5. Process Gaps & Action Items (Tracked for SOC 2 CC7)

| Work Item ID | Action Item Title | Assignee | Target Sprint |
|---|---|---|:---:|
| **[AB#34975]** | Formalize team on-call roster, backup rules, and escalation SLAs in Wiki | Constantin Pricochi | Sprint 2 |
| **[AB#34976]** | Publish Incident Severity Classification Rubric with Sev 1/Sev 2 thresholds | Dipak Singh | Sprint 2 |
| **[AB#34977]** | Embed runbook and KQL deep links directly into Azure Monitor Slack alert cards | Aditya Mehta | Sprint 2 |
| **[AB#34979]** | Implement automated Key Vault credential rotation and public network deny alerts | Maurice Kennedy | Sprint 2 |
