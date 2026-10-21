# Q&A CHEAT SHEET — Keep This Handy During the Meeting

> **How to use this file:** Keep this open on your side (NOT shared on screen).
> If someone asks a tough question, quickly scan this file for the answer.
> If the question is not listed here, use the "Golden Response" at the bottom.

---

## Questions From Constantin (Incident Commander / The Boss)

Constantin is the most senior person. He will ask big-picture, process, and
audit-related questions.

---

### Q1: "This procedure only covers DEMO. How do we know it will work in PROD?"

**Your answer:**

"That is a great point. DEMO mirrors our PROD architecture, so the procedures
are the same. We chose DEMO for this exercise to avoid any risk to production
systems. We can note in our Post-Incident Review that a live PROD drill is
planned for Q1 2027 as a follow-up."

---

### Q2: "The recovery check says 15 minutes stable. Who decides if 15 minutes is enough? What if the problem comes back at minute 16?"

**Your answer:**

"Good question. We should add a re-escalation rule to our procedure. If the
same metric breaches again within 1 hour of closing the incident, it should
automatically reopen at the same severity level. I will capture this as an
action item."

---

### Q3: "For the Key Vault incident — 18 attempts from an external IP is already a breach. Should we report this to the CISO or a regulatory body?"

**Your answer:**

"In a real scenario, yes. Under our security escalation protocol, you and
Maurice would jointly decide if the CISO liaison and legal team need to be
notified. We should add that as an explicit step in version 1.1 of our
procedure — a decision checkpoint for external reporting."

---

### Q4: "Where is the communication plan for customers? If 42 percent of requests are failing, customers are affected. Who tells them?"

**Your answer:**

"Currently, we do not have a customer-facing communication plan or status page.
This is a valid gap. I will add this as a new action item — we need a defined
process for who notifies the business team and customers during a Severity 1
outage."

---

### Q5: "How do we know the leaked credential was not used to access other resources beyond Key Vault?"

**Your answer:**

"Good point — we should check the blast radius. In a real incident, we would
run an Entra ID sign-in log query filtered by the compromised Service
Principal's App ID to see if it accessed anything else — storage accounts,
databases, or other Key Vaults. We should add this as a containment sub-step
in our procedure."

---

### Q6: "All action items say 'Sprint 2.' Auditors need actual dates, not sprint numbers."

**Your answer:**

"You are right. I will update all tickets with specific calendar due dates
when I create them in Azure DevOps after this call."

---

### Q7: "Should we be checking the attacker IP against threat intelligence feeds?"

**Your answer:**

"Yes. We should cross-reference IP 198.51.100.44 against Microsoft Threat
Intelligence and any SIEM feeds we have. If it is a known malicious actor,
that changes our severity and response. I will add a threat intelligence
check step to the security incident containment procedure."

---

## Questions From Maurice (Infrastructure Lead / Backup Responder)

Maurice is the hands-on engineer. He will ask practical, technical questions
about execution.

---

### Q1: "The scenario says I 'execute rollback in GitOps.' Which tool? What is the exact command?"

**Your answer:**

"Currently, it would be a manual `helm rollback` or a `git revert` on the
deployment manifest in our GitOps repository, followed by an ArgoCD sync.
We should document the exact rollback command in a runbook. This is part
of the reason for Action Item 3 — embedding runbook links in our alert
cards."

---

### Q2: "If I restrict the Key Vault to VNet-only access, will that break other services that connect over the public internet?"

**Your answer:**

"That is a real risk. Before applying this change in PROD, we need to audit
all services that access the Key Vault and confirm they all use Private
Endpoints or VNet service endpoints. In an active security breach, we would
lock it down first and fix any broken services second. But ideally, we should
have this audit done ahead of time."

---

### Q3: "What if I revoke the Service Principal and it turns out a real production service was using it? We would cause a self-inflicted outage."

**Your answer:**

"Exactly — that is why the first step should be to query Entra ID sign-in
logs for that Service Principal's App ID to see what is using it. In a true
emergency where data is actively being stolen, we revoke first and fix
dependencies second. But in a contained scenario, we check first."

---

### Q4: "Do I actually have the right permissions to rotate Key Vault secrets right now?"

**Your answer:**

"We should verify that. Everyone on the team should have 'Key Vault Secrets
Officer' RBAC role assigned on helios-dm0-kv. I will check and confirm this
after the call. If anyone is missing the permission, we will fix it."

---

### Q5: "The procedure relies on Slack for everything. What if Slack is down during the incident?"

**Your answer:**

"Good catch — that is a single point of failure. We should document a backup
communication channel, like a Microsoft Teams group call or a shared
emergency phone bridge. I will add this to the on-call documentation action
item."

---

## Questions From Aditya (Investigation Engineer)

Aditya handles observability and diagnostics. He will ask about the technical
details of alerts, queries, and monitoring.

---

### Q1: "42 percent failure rate — why is this classified as Severity 2 instead of Severity 1?"

**Your answer:**

"Our Severity Classification Matrix currently defines Severity 1 as a
customer-facing outage exceeding 50 percent, or a critical security breach.
At 42 percent, it technically falls under Severity 2 (Major). But you raise
a good point — the threshold may need to be lower. This is exactly what the
Severity Classification Rubric action item is designed to address. Let us
note your concern."

---

### Q2: "The alert evaluates every 5 minutes with a 15-minute window. That means up to 5 minutes delay before we even know. Is that acceptable?"

**Your answer:**

"For a Severity 2 alert, 5-minute evaluation is reasonable. But for anything
that could be Severity 1 (customer-facing), we should consider reducing the
evaluation frequency to 1 minute. Good observation — I will note this."

---

### Q3: "Do we actually have automated synthetic health monitoring, or is someone manually running curl each time?"

**Your answer:**

"Currently, it is manual. We should set up Azure Application Insights
availability tests or an external synthetic monitoring tool like Datadog
Synthetics. That way, the health check runs automatically and continuously."

---

### Q4: "In the Key Vault forensic query, we filter by IP address. But if the attacker used a VPN or changed IPs, we would miss them."

**Your answer:**

"Exactly right. We should also filter by the compromised Service Principal's
App ID (identity_claim_appid_g in our logs), not just the source IP. That
gives us identity-based detection, which catches the attacker even if they
change IP addresses."

---

### Q5: "The alert rule KQL has a hard-coded exclusion for 'telemetry-api' with a date. What is that? Does it expire?"

**Your answer:**

"Good catch — that was a temporary suppression added for a noisy API that
was generating false positives. The exclusion date is set to October 23,
2026, which is tomorrow. We should review it and either remove it if the
issue is fixed, or extend it with a documented reason."

---

## Surprise Curveball Questions (From Anyone)

---

### Q: "What if both scenarios happened at the same time? Could they be related?"

**Your answer:**

"That is a great observation. If the attacker gained access to the
redis-cache-connection secret and it was rotated while APIM was still
serving traffic, the APIM failure could be caused by the Key Vault
incident. We should always check for correlation between simultaneous
incidents. I will note this as a procedure improvement."

---

### Q: "What happens if Dipak is on vacation and nobody responds for 30 minutes?"

**Your answer:**

"Per our procedure, after 15 minutes of no response, Maurice is paged as
the Backup Responder. But honestly, right now, there is no automated
paging — someone has to manually notice the Slack message. This is a
genuine gap and one of the main reasons we need PagerDuty or a similar
automated on-call tool."

---

### Q: "We say the Post-Incident Review must happen within 5 business days. Who enforces that? What if we forget?"

**Your answer:**

"Currently, there is no automated reminder or enforcement. We should create
a recurring check — for example, an Azure DevOps automation that flags any
incident ticket without a linked Post-Incident Review after 5 business days.
I will add this as an action item."

---

### Q: "Have we tested our backup communication plan? What if Teams AND Slack are both down?"

**Your answer:**

"We have not. Our fallback would be direct phone calls using the emergency
contact numbers in our escalation roster. But this reinforces the need to
document those phone numbers in the Wiki — which is Action Item 1."

---

### Q: "Is this tabletop exercise sufficient for the SOC 2 audit, or do auditors want to see a real incident?"

**Your answer:**

"A tabletop exercise is an accepted and common method for SOC 2 CC7
compliance. Auditors want to see that the team has a documented procedure,
has practiced it, and has identified improvements. They do not require a
real production incident. The key is that our documentation is thorough
and shows genuine engagement — which is what we are doing today."

---

## THE GOLDEN RESPONSE — Use This When You Do Not Know the Answer

If someone asks a question that is not listed here, use one of these responses:

> **Option 1:**
> "That is an excellent point. Let me add it as an action item in our
> Post-Incident Review right now."
> *(Then type it into the shared Live Notes template.)*

> **Option 2:**
> "You are right, the current procedure does not cover that. Let us note
> it as a version 1.1 improvement."

> **Option 3:**
> "I will take an action item to research that and follow up with a
> proposal by next sprint."

> **NEVER say:** "I do not know" and leave it hanging.
> Always capture it as an action item. This turns a hard question into
> audit evidence that your team is actively identifying and fixing gaps.
