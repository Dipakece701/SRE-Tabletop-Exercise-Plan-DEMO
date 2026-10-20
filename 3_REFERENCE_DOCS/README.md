# AB#34970 — SRE Tabletop Exercise Master Workspace

**Work Item:** AB#34970: SRE tabletop: one fake incident on DEMO, written up (SOC 2)  
**Parent Feature:** `AB#31391` (*Observability: S1 fixes and DEMO baseline*)  
**Compliance Standard:** SOC 2 Type II — CC7.1, CC7.2, CC7.3 (Incident Detection, Management & Response)  
**Target Environment:** DEMO (`dm0`)  
**Hard Cutoff Date:** October 30, 2026 (Execution deadline: **October 28, 2026**)  
**Scheduled Execution Date & Time:** **Thursday, October 22, 2026 | 10:15 AM – 11:15 AM**  
**Facilitator & Scribe:** Dipak Singh  
**Participants:** Constantin Pricochi, Maurice Kennedy, Aditya Mehta, Dipak Singh  

---

## 👥 On-Call & Escalation Roster (Pre-PagerDuty Interim)

* **Primary Responder:** Dipak Singh (Backup: Aditya Mehta)
* **Backup Responder:** Maurice Kennedy
* **Escalation Lead / Incident Commander:** Constantin Pricochi
* **Security Escalation:** Constantin Pricochi
* **Escalation Rules:**
  * Missed Ack: If Primary doesn't ack within 15 min (Sev 1) / 30 min (Sev 2), Backup (Maurice) takes over.
  * Uncontained: If uncontained after 30 min, escalate directly to Constantin.
  * Security: Immediate joint escalation to Constantin and Maurice.

---

## 📂 Directory Contents & Document Index

| File | Purpose | When to Use |
|---|---|---|
| **00_Incident_Procedure_v1.md** | **Helios Incident Procedure v1.0**: 1-page SOP detailing severity matrix, responder/backup roster, escalation path, ack logging, incident records, and recovery verification. | Governing procedure document. |
| **01_Step_by_Step_Meeting_Guide.md** | Minute-by-minute facilitator script covering both Reliability (APIM 5xx) and Security (Key Vault) tracks. | Open during the meeting on Thursday. |
| **02_Scenario_Brief_Oct22.md** | Formal Scenario Brief with dual tracks (APIM 5xx surge + Key Vault unauthorized access spike). | Send to Constantin on Wednesday (Oct 21). |
| **03_Calendar_Invite_Details.md** | Pre-formatted meeting title, attendee emails, description, and agenda. | Send calendar invite to team. |
| **04_Live_Notes_and_PIR_Template.md** | Pre-structured markdown template to share on screen and record who acked, incident record, and recovery check. | Share screen and type live on Thursday. |
| **05_Technical_Telemetry_and_KQL_Reference.md** | Technical reference: APIM alert definition, Key Vault audit KQL queries, and Grafana panels. | Reference during technical discussion. |

---

## 🎯 Executive Checklist & Deadlines

- [x] **Incident Procedure v1.0 Written**: Documented roles, backup, escalation path, ack protocol, incident records, and recovery verification.
- [x] **Dual Scenarios Built**: Reliability (APIM 5xx) and Security (Key Vault unauthorized access spike).
- [x] **Meeting Slot Selected**: Thursday, October 22, 2026 @ 10:15 AM – 11:15 AM.
- [ ] **Deliver Scenario Brief & Procedure**: Send `00_Incident_Procedure_v1.md` and `02_Scenario_Brief_Oct22.md` to Constantin on Slack by Wednesday, Oct 21.
- [ ] **Send Calendar Invite**: Send meeting invitation to Constantin, Maurice, and Aditya.
- [ ] **Execute Tabletop Call**: Facilitate the 60-minute session on Thursday, Oct 22 using `01_Step_by_Step_Meeting_Guide.md`.
- [ ] **Publish Post-Incident Review**: Post write-up to ADO Wiki (`/QRE/Incident Retrospectives`).
- [ ] **Create Follow-up Tickets**: Log the 4 identified gap tickets in ADO (AB#34975, AB#34976, AB#34977, AB#34979).
- [ ] **Resolve Work Item**: Link evidence to AB#34970 and close ticket by Oct 28.
