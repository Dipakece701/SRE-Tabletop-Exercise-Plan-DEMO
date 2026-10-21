# LIVE NOTES TEMPLATE — Share This On Screen During the Call

> **INSTRUCTIONS:** Share this file on your screen during the meeting.
> Fill in the blanks and tick the checkboxes LIVE as the team discusses each point.
> Leave everything empty before the call starts.

---

# SRE Tabletop Exercise — Live Meeting Notes

- **Exercise Date:** Thursday, October 22, 2026
- **Time:** 10:15 AM to 11:15 AM (60 minutes)
- **Governing Procedure:** Incident Response Procedure v1.0
- **Target Environment:** DEMO (dm0)
- **Work Item Reference:** AB#34970 (SOC 2 CC7 Audit Evidence)

---

## 1. Attendees and Roles

| Role | Name | Contact |
|---|---|---|
| **Incident Commander** | _______________ | Slack: _______________ |
| **Primary Responder (Facilitator)** | _______________ | Slack: _______________ |
| **Backup Responder** | _______________ | Slack: _______________ |
| **Investigation Engineer** | _______________ | Slack: _______________ |
| **Security Escalation Lead** | _______________ | Slack: _______________ |

---

## 2. Scenario A: App Gateway Error Surge (Reliability Incident)

**Incident ID:** _______________
**Incident Record (Azure DevOps ticket):** _______________
**Severity Level:** _______________

### Who responded to the alert?

- **Responded by:** _______________
- **Time of response:** _______________ UTC
- **How they responded:** _______________
- **Backup responder (if Primary did not respond):** _______________

### Timeline of Events:

| Time (UTC) | What Happened | Who Did It |
|---|---|---|
| _____ | Alert fired: _________________________________ | System (automated) |
| _____ | Alert acknowledged in Slack | _______________ |
| _____ | Incident record created in Azure DevOps | _______________ |
| _____ | Diagnostic investigation completed | _______________ |
| _____ | Incident Commander authorized rollback | _______________ |
| _____ | Rollback executed | _______________ |

### Root Cause Found:

- **Which service failed?** _______________
- **What type of error?** _______________
- **Was the failure isolated to one service or widespread?** _______________

### Recovery Verification (4-Point Checklist):

- [ ] **Metric check:** Error rate dropped from ___% to ___% and stayed stable for ___ minutes.
- [ ] **Health check:** Health endpoint returned HTTP ___ in ___ms.
- [ ] **Log check:** Diagnostic query confirmed ___ errors in the last ___ minutes.
- [ ] **Sign-off:** Recovery evidence posted to Slack. Incident record marked as _______________.

---

## 3. Scenario B: Key Vault Security Alert (Security Incident)

**Incident ID:** _______________
**Incident Record (Azure DevOps ticket):** _______________
**Severity Level:** _______________

### Who responded to the alert?

- **Responded by:** _______________
- **Time of response:** _______________ UTC
- **Escalated to:** _______________
- **Reason for escalation:** _______________

### Timeline of Events:

| Time (UTC) | What Happened | Who Did It |
|---|---|---|
| _____ | Security alert fired from Microsoft Defender | System (automated) |
| _____ | Alert acknowledged in Slack | _______________ |
| _____ | Joint security escalation triggered | _______________ |
| _____ | Incident record created in Azure DevOps | _______________ |
| _____ | Diagnostic investigation completed | _______________ |
| _____ | Compromised credential disabled | _______________ |
| _____ | Key Vault firewall locked down | _______________ |
| _____ | Secret rotated and services restarted | _______________ |

### Attacker Details:

- **Attacker IP address:** _______________
- **Number of unauthorized attempts:** _______________
- **Targeted secret name:** _______________
- **How the credential was compromised:** _______________

### Containment Actions Taken:

- [ ] Compromised Service Principal credential disabled and deleted in Entra ID.
- [ ] Key Vault firewall restricted to trusted internal networks only.
- [ ] Targeted secret rotated (new password generated).
- [ ] Dependent services restarted with the new secret.

### Recovery Verification (4-Point Checklist):

- [ ] **Audit log check:** Confirmed ___ requests from attacker IP in the last ___ minutes.
- [ ] **Service health check:** Legitimate services authenticated with new secret (HTTP ___).
- [ ] **Defender status:** Microsoft Defender marked incident as _______________.
- [ ] **Security sign-off:** _______________ reviewed audit evidence and approved closure.

### Additional Security Checks (if discussed):

- [ ] Checked if the attacker IP hit any other Azure resources: _______________
- [ ] Checked Entra ID sign-in logs for the compromised Service Principal: _______________
- [ ] Checked threat intelligence feeds for the attacker IP: _______________
- [ ] CISO notification required? _______________

---

## 4. What Went Well

1. _______________________________________________________________
2. _______________________________________________________________
3. _______________________________________________________________
4. _______________________________________________________________

*(Add more rows as needed during the discussion.)*

---

## 5. Gaps Found and Action Items

| # | Action Item | Who is responsible? | Target date |
|---|---|---|---|
| 1 | _______________________________________ | _______________ | _______________ |
| 2 | _______________________________________ | _______________ | _______________ |
| 3 | _______________________________________ | _______________ | _______________ |
| 4 | _______________________________________ | _______________ | _______________ |
| 5 | _______________________________________ | _______________ | _______________ |
| 6 | _______________________________________ | _______________ | _______________ |

---

## 6. Customer and Stakeholder Communication

- **Was customer communication needed for either scenario?** _______________
- **Who is responsible for customer notifications?** _______________
- **Do we have a status page?** _______________
- **Action item to improve this?** _______________

---

## 7. Sign-Off

By signing below, participants confirm they attended and participated in the
SRE Tabletop Exercise for SOC 2 CC7 audit compliance.

| Name | Role | Confirmed (Yes/No) |
|---|---|---|
| Constantin Pricochi | Incident Commander | |
| Maurice Kennedy | Backup Responder | |
| Aditya Mehta | Investigation Engineer | |
| Dipak Singh | Primary Responder / Facilitator | |

---

> **After the meeting:** Copy this completed document and attach it to
> Azure DevOps work item AB#34970 as audit evidence.
