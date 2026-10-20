# 01 — Step-by-Step Facilitator Meeting Guide

**Meeting:** SRE Tabletop Exercise: Reliability & Security Simulation (SOC 2 CC7)  
**Date & Time:** Thursday, October 22, 2026 | 10:15 AM – 11:15 AM (60 minutes)  
**Governing SOP:** `00_Incident_Procedure_v1.md`  
**Facilitator & Scribe:** Dipak Singh  
**Participants:** Constantin Pricochi, Maurice Kennedy, Aditya Mehta, Dipak Singh  

---

## 🖥️ Screen Setup (Before Joining the Call)

Open these **3 browser tabs / windows**:
1. **Window 1 (Main Screen Share)**: Open `04_Live_Notes_and_PIR_Template.md` in your editor. **Share this window on your screen** so everyone sees you typing notes live.
2. **Window 2 (Procedure Reference)**: Open `00_Incident_Procedure_v1.md`.
3. **Window 3 (Technical Telemetry Reference)**: Open `05_Technical_Telemetry_and_KQL_Reference.md`.

---

## ⏱️ Minute-by-Minute Meeting Playbook

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ 10:15 - 10:20 │ Phase 1: Welcome & Incident Procedure v1.0 Overview        │
├─────────────────────────────────────────────────────────────────────────────┤
│ 10:20 - 10:35 │ Phase 2: Scenario A — APIM 5xx Surge (Ack, Record, Recovery)│
├─────────────────────────────────────────────────────────────────────────────┤
│ 10:35 - 10:50 │ Phase 3: Scenario B — Key Vault Security Spike (Ack, Rotate)│
├─────────────────────────────────────────────────────────────────────────────┤
│ 10:50 - 11:15 │ Phase 4: Post-Incident Review (PIR) & Action Items (SOC 2)  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### Phase 1: Welcome & Incident Procedure v1.0 (10:15 – 10:20)

* **What you do:** Share screen showing `04_Live_Notes_and_PIR_Template.md`.
* **What you say:**
  > *"Hi everyone, thanks for joining on time. Today we are conducting our SRE Tabletop Exercise for work item AB#34970 to provide evidence for our SOC 2 CC7 audit.*  
  > *Before we begin our simulated injects, let's establish our baseline under Incident Procedure v1.0:*  
  > *1. Primary Responder: Dipak Singh*  
  > *2. Backup Responder: Maurice Kennedy*  
  > *3. Incident Commander / Escalation Lead: Constantin Pricochi*  
  > *4. Interim Escalation Rule: If Primary doesn't ack in 15 mins, Maurice takes over. If uncontained after 30 mins, escalate directly to Constantin.*  
  > *Today we will walk through two paired scenarios: an APIM 5xx reliability surge, and a Key Vault unauthorized access security incident. For both, we will record who acked, create the incident record, and verify recovery. Let's begin."*

---

### Phase 2: Scenario A — APIM 5xx Surge (10:20 – 10:35)

* **What you say:**
  > *"At 10:18 UTC, an automated alert fires in `#alerts-dm0-ops` from `ag-helios-dm0-ops`:*  
  > 🚨 **Alert:** `helios-dm0-gateway-failure-rate` — *42% of calls failing with 5xx over the last 15 minutes.*  
  >  
  > *Step 1: Who acked?*  
  > *As Primary Responder, I acknowledge in Slack with 👀 and post: 'ACKNOWLEDGED by @Dipak Singh at 10:20 UTC. Opening triage bridge. Declaring Sev 2 incident.'*  
  >  
  > *Step 2: Incident Record?*  
  > *I open ADO and log incident record AB#34974 under `Helios\QRE` tagged `incident`, `sev-2`.*  
  >  
  > *Now, Aditya/Maurice: Aditya runs the KQL diagnostic query in Log Analytics workspace `helios-dm0-logs`. The query reveals that `ontology-event-processor` is throwing HTTP 504 gateway timeouts (exceeding 30s), while `plan-narration` is 100% healthy.*  
  >  
  > *Mitigation: Constantin, as Incident Commander, do you authorize restarting the `ontology-event-processor` container or rolling back to the previous Helm digest?"*
* **Team response:** Constantin authorizes rollback/restart. Maurice executes rollback.
* **What you say:**
  > *"Now, Step 3: How do we check recovery before closing the incident?*  
  > *Under Procedure v1.0, we execute our 4-point verification checklist:*  
  > *1. APIM 5xx metric dropped from 42% to 0.08% for > 15 consecutive minutes.*  
  > *2. Synthetic probe `curl -i GET /ontology-event-processor/health` returns HTTP 200 OK in 48ms.*  
  > *3. KQL in `AzureDiagnostics` confirms zero 504 errors in the last 10 minutes.*  
  > *4. Responder signs off in Slack and marks AB#34974 Resolved."*

---

### Phase 3: Scenario B — Key Vault Security Spike (10:35 – 10:50)

* **What you say:**
  > *"Now let's switch to our Security track at 10:25 UTC.*  
  > 🚨 **Alert from Defender for Cloud:** *Key Vault `helios-dm0-kv`: 18 unauthorized secret retrieval attempts (HTTP 403) detected within 5 minutes from untrusted external IP `198.51.100.44` targeting secret `redis-cache-connection`.*  
  >  
  > *Step 1: Who acked?*  
  > *I acknowledge at 10:27 UTC in Slack. Because this is a credential/secret alert, our procedure dictates immediate escalation to Constantin and Maurice.*  
  >  
  > *Step 2: Incident Record?*  
  > *We create ADO incident record AB#34978 (`[INCIDENT - Sev 1 Security] Anomalous Key Vault access attempts on helios-dm0-kv`).*  
  >  
  > *Step 3: Containment actions:*  
  > *Maurice checks Microsoft Entra ID and disables/deletes the compromised Service Principal credential.*  
  > *Dipak restricts Key Vault network access to trusted VNets only and rotates the `redis-cache-connection` secret.*  
  >  
  > *Step 4: How do we check recovery for this security incident?*  
  > *Let's review our verification criteria:*  
  > *1. Audit log query in `AzureDiagnostics` confirms 0 requests from `198.51.100.44` over the last 30 minutes.*  
  > *2. Legitimate container workloads successfully authenticate and retrieve the rotated secret via Managed Identity / Workload Identity.*  
  > *3. Microsoft Defender incident status transitions to Mitigated.*  
  > *4. Constantin signs off on security containment."*

---

### Phase 4: PIR & Action Items for SOC 2 (10:50 – 11:15)

* **What you say:**
  > *"To finalize our SOC 2 CC7 audit requirements, we need to capture our retrospective live:*  
  > *1. What went well? (Quick detection, clear ack and role ownership, technical recovery verification).*  
  > *2. What gaps did we discover?*  
  > *  - Gap 1: Formalize team on-call roster, backup rules, and escalation SLAs in Wiki.*  
  > *  - Gap 2: Publish formal Incident Severity Classification Rubric with Sev 1/Sev 2 thresholds.*  
  > *  - Gap 3: Embed runbook and KQL deep links directly into Azure Monitor Slack alert cards.*  
  > *  - Gap 4: Implement automated Key Vault credential rotation and public network deny alerts.*  
  >  
  > *We will create 4 follow-up ADO work items (AB#34975, AB#34976, AB#34977, AB#34979), publish these notes to the ADO Wiki under `/QRE/Incident Retrospectives/`, and link to AB#34970."*
