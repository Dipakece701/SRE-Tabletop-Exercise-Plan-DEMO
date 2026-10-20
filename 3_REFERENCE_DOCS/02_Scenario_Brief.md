# 02 — SRE Tabletop Scenario Brief

**Work Item:** AB#34970  
**Parent Feature:** `AB#31391` (*Observability: S1 fixes and DEMO baseline*)  
**Standard Operating Procedure:** [`00_Incident_Procedure_v1.md`]  
**Compliance Standard:** SOC 2 Type II — CC7.1, CC7.2, CC7.3 (Incident Detection, Management & Response)  
**Target Environment:** DEMO (`dm0`)  
**Facilitator & Scribe:** Dipak Singh  
**Participants:** Constantin Pricochi, Maurice Kennedy, Aditya Mehta, Dipak Singh  

---

## 1. On-Call & Escalation Roster (Pre-PagerDuty Interim)

Per **Incident Procedure v1.0**, the current team escalation path without automated paging tooling is:

| Role | Primary Responder | Backup Responder | Escalation Lead |
|---|---|---|---|
| **Incident Commander (IC)** | **Constantin Pricochi** | **Maurice Kennedy** | Head of Engineering |
| **Primary Triage / On-Call SRE** | **Dipak Singh** | **Aditya Mehta** | Constantin Pricochi |
| **Infrastructure Lead** | **Maurice Kennedy** | **Dipak Singh** | Constantin Pricochi |
| **Security Escalation** | **Constantin Pricochi** | **Maurice Kennedy** | Qcells CISO Liaison |

* **Escalation Rules:**
  * If Primary does not acknowledge within **15 minutes** (Sev 1) or **30 minutes** (Sev 2), Backup Responder (Maurice) takes over immediately.
  * If unresolved/uncontained after **30 minutes**, escalate directly to Incident Commander (Constantin).
  * Any security/data compromise triggers immediate joint escalation to Constantin and Maurice.

---

## 2. Tabletop Structure: Dual-Scenario Simulation

To comprehensively test both **Operational Availability** and **Information Security** under SOC 2 CC7, this exercise walks through two paired incident tracks:
1. **Scenario A (Reliability):** APIM Gateway 5xx Failure Rate Surge (`helios-dm0-apim`)
2. **Scenario B (Security):** Key Vault Unauthorized Access Spike & Credential Compromise (`helios-dm0-kv`)

Both scenarios explicitly record:
* **Who Acked** (responder name, timestamp, and Slack channel acknowledgment).
* **The Incident Record** (Azure DevOps work item ID, severity, and tags).
* **How We Checked Recovery** (4-point technical verification checklist before resolution).

---

## 3. Scenario A (Reliability): APIM 5xx Surge

### Baseline & Ingestion
* **Resource:** `helios-dm0-apim` in `helios-dm0-us-west3-rg` (Subscription: `Helios - Demo`).
* **Alert Rule:** `helios-dm0-gateway-failure-rate` (`Category == "GatewayLogs"`, 15-min window, threshold $> 10\%$).
* **Notification Route:** Action Group `ag-helios-dm0-ops` $\rightarrow$ Slack `#alerts-dm0-ops`.

### Simulation Timeline & Injects
1. **10:18 UTC — Detection:** Alert fires to `#alerts-dm0-ops` reporting 42% 5xx error rate on `helios-dm0-apim`.
2. **10:20 UTC — Who Acked:**
   * **Acknowledged by:** **Dipak Singh** (Primary Responder) via 👀 reaction + Slack thread post:
     > *"ACKNOWLEDGED by @Dipak Singh at 10:20 UTC. Opening triage bridge. Declaring Sev 2 incident."*
3. **10:22 UTC — The Incident Record:**
   * Logged in Azure DevOps under `Helios\QRE`: **AB#34974** (`[INCIDENT - Sev 2] APIM Gateway 5xx Spike on helios-dm0-apim`). Assigned to Dipak Singh.
4. **10:32 UTC — Investigation:**
   * KQL in `helios-dm0-logs` (`AzureDiagnostics | where Category == "GatewayLogs"`) confirms error isolated to `ontology-event-processor` with upstream HTTP 504 (timeout > 30s).
5. **10:45 UTC — Mitigation:**
   * Incident Commander (Constantin) authorizes pod restart/rollback of `ontology-event-processor`.
6. **10:50 UTC — How We Checked Recovery:**
   * **Criterion 1 (Metric):** APIM 5xx failure rate drops from 42% to 0.08% and remains stable for > 15 minutes.
   * **Criterion 2 (Synthetic Probe):** `curl -i -X GET https://helios-dm0-apim.azure-api.net/ontology-event-processor/health` returns `HTTP 200 OK` in 48ms.
   * **Criterion 3 (Diagnostic KQL):** Log Analytics confirms 0 gateway 504 errors in last 10 minutes.
   * **Criterion 4 (Sign-off):** Dipak posts recovery proof to Slack; status updated to Resolved on AB#34974.

---

## 4. Scenario B (Security): Key Vault Unauthorized Access Spike

### Baseline & Ingestion
* **Resource:** `helios-dm0-kv` (Azure Key Vault in `helios-dm0-us-west3-rg`).
* **Alert Rule:** `helios-dm0-kv-unauthorized-access` (Alert on $\ge 5$ HTTP 401/403 `SecretGet` calls from unrecognized IP/Principal within 5 minutes).
* **Notification Route:** Microsoft Defender for Cloud $\rightarrow$ Action Group `ag-helios-dm0-ops` $\rightarrow$ Slack `#alerts-dm0-ops`.

### Simulation Timeline & Injects
1. **10:25 UTC — Detection:** Defender for Cloud alert fires:
   > 🚨 *[SECURITY Sev 1] Key Vault `helios-dm0-kv`: 18 unauthorized secret retrieval attempts (HTTP 403 Forbidden) detected from external IP `198.51.100.44` targeting secret `redis-cache-connection`.*
2. **10:27 UTC — Who Acked:**
   * **Acknowledged by:** **Dipak Singh** (Primary Responder) via 👀 + Slack thread:
     > *"ACKNOWLEDGED by @Dipak Singh at 10:27 UTC. Paging @Constantin Pricochi. Initiating immediate security lockdown."*
   * Escalated immediately to **Constantin Pricochi** and **Maurice Kennedy**.
3. **10:29 UTC — The Incident Record:**
   * Logged in Azure DevOps under `Helios\QRE`: **AB#34978** (`[INCIDENT - Sev 1 Security] Anomalous Key Vault access attempts on helios-dm0-kv`). Tags: `security`, `sev-1`, `soc2-cc7`.
4. **10:35 UTC — Diagnostic Investigation:**
   * Audit query in `AzureDiagnostics | where ResourceProvider == "MICROSOFT.KEYVAULT"` reveals an expired service principal credential was leaked or queried from an untrusted public IP.
5. **10:42 UTC — Containment & Mitigation:**
   * **Immediate Revocation:** Maurice revokes and deletes the compromised service principal credential in Microsoft Entra ID.
   * **Network Hardening:** Key Vault firewall restricted to enforce private endpoint / trusted Azure VNet subnets only.
   * **Secret Rotation:** Dipak rotates `redis-cache-connection` secret in Key Vault and restarts dependent container workloads with updated version.
6. **10:52 UTC — How We Checked Recovery:**
   * **Criterion 1 (Audit Log Zero):** Key Vault audit log query in `helios-dm0-logs` shows 0 requests (401/403 or 200) from IP `198.51.100.44` post-containment:
     ```kql
     AzureDiagnostics
     | where ResourceProvider == "MICROSOFT.KEYVAULT"
     | where CallerIPAddress == "198.51.100.44"
     | where TimeGenerated > ago(30m)
     ```
   * **Criterion 2 (Workload Health):** Legitimate backend workloads (`ontology-event-processor`, `cost-ingestion`) verified pulling new secret version via Workload Identity / Managed Identity with HTTP 200 OK.
   * **Criterion 3 (Defender Status):** Microsoft Defender for Cloud security incident marked *Mitigated - Credential Rotated & Network Isolated*.
   * **Criterion 4 (Sign-off):** Constantin reviews audit evidence and signs off on incident closure at 10:55 UTC.

---

## 5. Expected Audit Artifacts for AB#34970

1. **Incident Procedure v1.0:** [`00_Incident_Procedure_v1.md`]
2. **Dual Incident Retrospectives:** Live write-up covering both APIM 5xx and Key Vault Security incidents.
3. **Documented Gaps & Remediation Work Items:**
   * `AB#34975`: Document SRE on-call rotation and escalation matrix.
   * `AB#34976`: Publish formal Incident Severity Classification Rubric.
   * `AB#34977`: Update gateway failure alert with direct runbook URL.
   * `AB#34979`: Automate automated secret rotation & Key Vault firewall audit alerts.
4. **Sign-off Sheet:** Signed by Constantin, Maurice, Aditya, and Dipak.
