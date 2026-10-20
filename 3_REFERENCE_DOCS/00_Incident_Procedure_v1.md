# Helios Incident Response Procedure (v1.0)

**Standard Operating Procedure (SOP) — Observability & SRE Team**  
**Document ID:** `SOP-QRE-INC-001` | **Version:** 1.0  
**Effective Date:** October 2026 | **Review Cycle:** Annual  
**Compliance Standard:** SOC 2 Type II (CC7.1, CC7.2, CC7.3, CC7.4)  
**Scope:** DEMO (`dm0`), DEV (`dv0`), QA (`qa0`), and PROD (`pd0`) environments  

---

## 1. Roles & Escalation Roster (Pre-PagerDuty Interim)

Until automated on-call tooling (e.g. PagerDuty) is deployed, on-call paging and escalations operate via designated Slack mentions, Teams bridges, and direct phone escalation:

| Incident Role | Primary Assignment | Backup Assignment | Escalation Contact |
|---|---|---|---|
| **Primary Responder** (First Ack) | **Dipak Singh** | **Aditya Mehta** | Slack `@Dipak Singh` / Direct Phone |
| **Backup Responder** (Secondary) | **Maurice Kennedy** | **Dipak Singh** | Slack `@Maurice Kennedy` / Direct Phone |
| **Incident Commander (IC) / Escalation Lead** | **Constantin Pricochi** | **Maurice Kennedy** | Slack `@Constantin Pricochi` / Direct Phone |
| **Security Escalation Lead** *(Security Incidents)* | **Constantin Pricochi** | **Maurice Kennedy** | Direct Escalation Call / Slack `#security-ops` |

### Escalation Trigger Criteria
* **Missed Ack:** If Primary Responder does not acknowledge an alert within **15 minutes** (Sev 1) or **30 minutes** (Sev 2), the Backup Responder is immediately paged.
* **Uncontained Incident:** If root cause is not identified or incident remains uncontained within **30 minutes** of triage, escalate immediately to **Constantin Pricochi**.
* **Security / Data Incident:** Any unauthorized secret access, credential exposure, or breach alert requires immediate joint escalation to **Constantin Pricochi** and **Maurice Kennedy**.

---

## 2. Severity Classification Matrix

| Severity | Definition & Impact Criteria | Ack Target | Target Update Cadence | PIR Required? |
|---|---|---|---|---|
| **Sev 1 (Critical)** | Customer-facing outage, critical telemetry pipeline failure > 50%, unauthorized data access or Key Vault secret compromise. | **< 15 minutes** | Every 30 mins | **Yes** (PIR within 5 days) |
| **Sev 2 (Major)** | Component failure (e.g., APIM 5xx > 10%, single ingestion pipeline failure, non-critical replica degradation, isolated security alert). | **< 30 minutes** | Every 60 mins | **Yes** (PIR within 5 days) |
| **Sev 3 (Minor)** | Internal tooling degraded, telemetry dropped without service impact, single pod restart, informational security scan alert. | **< 2 hours** | At resolution | Optional / Standup note |

---

## 3. Incident Lifecycle Workflow (Step-by-Step)

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  1. Detection    │ ──> │ 2. Acknowledge   │ ──> │ 3. Open Incident │
│  (Monitor Alert) │     │ (Slack & Channel)│     │ Record (ADO)     │
└──────────────────┘     └──────────────────┘     └──────────────────┘
                                                            │
┌──────────────────┐     ┌──────────────────┐               ▼
│ 6. PIR & Gaps    │ <── │ 5. Recovery      │ <── ┌──────────────────┐
│ (SOC 2 CC7 Wiki) │     │ Verification     │     │ 4. Containment   │
└──────────────────┘     └──────────────────┘     │ & Mitigation     │
                                                  └──────────────────┘
```

### Step 1: Detection & Alert Ingestion
* Alerts fire from Azure Monitor / Microsoft Defender for Cloud into Slack `#alerts-dm0-ops` (or environment-specific channel).
* Each alert includes: Resource ID, Metric/Query condition, Severity, and Runbook link.

### Step 2: Acknowledgement Protocol ("Who Acked")
* The Primary Responder reacts to the Slack alert message with 👀 (`:eyes:`).
* The Primary Responder posts an explicit acknowledgment reply within the alert thread:
  ```text
  ACKNOWLEDGED by @Dipak Singh at 10:20 UTC. Investigating APIM failure rate spike.
  ```
* If no ack is posted within 15 minutes, Backup Responder (@Maurice Kennedy) steps in and acks.

### Step 3: Incident Record Creation ("The Incident Record")
Within 15 minutes of acknowledgement, the responder logs an Incident Work Item in Azure DevOps:
* **Work Item Type:** `Issue` (or `User Story` with tag `incident`)
* **Project / Area Path:** `Helios\QRE`
* **Title:** `[INCIDENT - Sev <X>] <Brief Description of Impact and Resource>`
* **Required Fields:**
  * **Assigned To:** Incident Commander / Lead Investigator
  * **Severity:** Sev 1 / Sev 2 / Sev 3
  * **Tags:** `incident`, `sev-1` / `sev-2`, `tabletop` (if simulated)
  * **Description:** Impacted resource, trigger time, initial symptoms, and active Slack thread/Teams call link.

### Step 4: Containment & Technical Mitigation
* **Reliability Incidents:** Rollback problematic deployment (`helm rollback` / git revert), scale out healthy replicas, activate APIM circuit breaker, or reroute traffic.
* **Security Incidents:** Immediately revoke compromised credentials/SAS tokens, disable rogue service principal, rotate Key Vault secret/key, and block offending IP at APIM/Firewall.

### Step 5: Recovery Verification Protocol ("How We Checked Recovery")
Before an incident can be downgraded or closed, the investigator and Incident Commander must complete and record the **4-point verification checklist**:
1. **Metric Return to Baseline:** Verify primary SLI/metric (e.g. APIM 5xx rate < 0.5%, pod restarts = 0, unauthorized calls = 0) has returned to baseline and remained stable for at least **15 consecutive minutes**.
2. **Synthetic Health Probe:** Execute end-to-end synthetic probe or health check against the live endpoint:
   ```bash
   curl -i -X GET "https://helios-dm0-apim.azure-api.net/health" -H "Ocp-Apim-Subscription-Key: $KEY"
   ```
3. **Audit / Diagnostic Log Confirmation:** Run KQL query in Log Analytics (`helios-dm0-logs`) confirming zero abnormal errors or unauthorized requests in the post-mitigation window.
4. **Responder Sign-off:** Primary responder posts recovery verification evidence (metric screenshot or KQL output) into the Slack thread:
   ```text
   RECOVERY VERIFIED by @Dipak Singh at 10:55 UTC:
   - APIM 5xx rate down from 42% to 0.08% over last 15m.
   - Pod ontology-event-processor running 2/2 healthy.
   - Status updated to RESOLVED on ADO Incident #<ID>.
   ```

### Step 6: Post-Incident Review (PIR) & SOC 2 CC7 Evidence
* **Timeline:** PIR meeting held within **5 business days** of resolution for all Sev 1 and Sev 2 incidents.
* **Documentation:** Completed PIR posted to Azure DevOps Wiki under:  
  `Helios Wiki / QRE / Incident Retrospectives / YYYY-MM-DD-<incident-title>`
* **Remediation Action Items:** Every identified gap must have a dedicated follow-up Work Item in ADO linked to the Incident Record with an assigned engineer and due sprint.
* **Audit Trail:** Preserved for SOC 2 CC7 auditors as proof of operational incident management.
