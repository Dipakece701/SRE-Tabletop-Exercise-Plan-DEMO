# YOUR MEETING SCRIPT — Read This Line by Line During the Call

> **What is this meeting?** A 60-minute practice drill (like a fire drill) where the team
> discusses two fake emergency scenarios. No real systems will be changed.
>
> **Why are we doing this?** To pass our SOC 2 security audit before October 30.
> The auditors need proof that our team has a clear plan for handling emergencies.
>
> **Your job:** Read this script out loud, ask questions to the team, and fill in the
> Live Notes template on your shared screen.

---

## YOUR SETUP (5 Minutes Before the Call)

1. **Join the Teams/Zoom call.**
2. **Share your screen** showing the file: `03_LIVE_NOTES_TEMPLATE.md`
   (Open it in Notepad or VS Code so the team can see you typing notes live.)
3. **Keep THIS script** on your second monitor, or in a separate window that is NOT shared.
   (This is your teleprompter. Only you should see this.)
4. **Keep a browser tab open** to the Azure DevOps work item:
   `https://dev.azure.com/qcellsces/Helios/_workitems/edit/34970`

---

## STEP 1: OPENING THE MEETING (Minute 00:00 to 05:00)

> Wait 1 to 2 minutes for everyone to join, then read the following out loud:

**Say this:**

"Hi everyone, thanks for joining on time. Today we are running our 60-minute
SRE Tabletop Exercise for work item AB#34970. This is to provide evidence for
our SOC 2 CC7 audit, which has a hard cutoff on October 30.

The goal today is to walk through our newly drafted Incident Response Procedure
(version 1.0) on the DEMO environment, and test our team's response from the
moment we detect a problem, all the way to verifying that things are fixed.

This is purely a discussion exercise. No live changes will be made to any systems.

For this simulation, our team roles are:

- Primary Responder: Dipak Singh (that is me).
- Backup Responder: Maurice Kennedy.
- Incident Commander: Constantin Pricochi.
- Security Escalation: Constantin Pricochi and Maurice Kennedy together.

Our escalation rules are:
- If the Primary Responder does not respond to an alert within 15 minutes for a
  critical incident, or 30 minutes for a major incident, the Backup Responder
  (Maurice) steps in immediately.
- If the issue is not contained within 30 minutes, we escalate directly to
  Constantin.
- Any security or data breach triggers an immediate joint escalation to both
  Constantin and Maurice.

I will facilitate the discussion and capture our notes live on screen.

We will walk through two scenarios today:
1. An app gateway error surge (our APIM service throwing 5xx errors).
2. A Key Vault security alert (unauthorized access attempts).

Let us begin with Scenario A."

> **On your shared screen:** Fill in the Attendees section of the Live Notes template now.
> Type in everyone's name and role.

---

## STEP 2: SCENARIO A — APP GATEWAY ERROR SURGE (Minute 05:00 to 20:00)

> This is the "Reliability" scenario. An alert fires because our API gateway is
> returning errors to users.

**Say this:**

"At 10:18 UTC, an automated alert fires in our Slack channel #alerts-dm0-ops.

The alert says: 'helios-dm0-gateway-failure-rate' — 42 percent of incoming
requests on our APIM gateway are failing with server errors (HTTP 5xx) over the
last 15 minutes. Our threshold for this alert is 10 percent, so 42 percent is
well above the danger line.

Let us walk through how we respond to this step by step.

**Step 1 — Who responded to the alert?**

As Primary Responder, I acknowledge the alert in Slack at 10:20 UTC, which is
2 minutes after it fired. I post:
'ACKNOWLEDGED by Dipak Singh at 10:20 UTC. Opening triage bridge. Declaring
this a Severity 2 (Major) incident.'

Maurice, as backup, per our procedure — if I had not responded within 15
minutes, you would have stepped in immediately. Does that make sense?"

> Pause for Maurice to confirm. Then continue:

"**Step 2 — Creating the incident record.**

At 10:22 UTC, I create a work item in Azure DevOps (ticket number AB#34974)
under the Helios QRE project. I tag it as 'incident' and 'sev-2'."

> **On your shared screen:** Fill in the Scenario A section of the Live Notes:
> - Who responded: Dipak Singh, 10:20 UTC
> - Incident record: AB#34974, Severity 2

"**Step 3 — Investigation and finding the root cause.**"

> Turn to Aditya and ask:

"Aditya, you run the diagnostic query in our Log Analytics workspace
(helios-dm0-logs). The query results reveal that one specific service called
'ontology-event-processor' is timing out with HTTP 504 errors (requests taking
longer than 30 seconds). Meanwhile, another service called 'plan-narration' is
completely healthy.

So the problem is isolated to just one service. What does this tell us?"

> Let Aditya answer. Then turn to Constantin:

"Constantin, as Incident Commander, do you authorize us to restart the
ontology-event-processor pod, or roll it back to the previous working version?"

> Wait for Constantin to answer. He will likely say "Yes, approved" or "Go ahead
> with the rollback." Then continue:

"Great. Maurice, you execute the rollback through our GitOps pipeline.

**Step 4 — How do we verify that the fix worked before closing the incident?**

Under our Incident Procedure version 1.0, we do not just assume things are
fixed. We run a 4-point verification checklist:

1. **Metric check:** The APIM error rate drops from 42 percent down to 0.08
   percent, and stays stable for at least 15 consecutive minutes.
2. **Health check:** We hit the health endpoint of ontology-event-processor
   and it returns HTTP 200 OK (meaning it is alive and healthy).
3. **Log check:** Our diagnostic query confirms zero 504 timeout errors in
   the last 10 minutes.
4. **Sign-off:** I post the recovery proof into Slack and mark the incident
   record as Resolved in Azure DevOps.

Does everyone agree this checklist is sufficient for closing a Severity 2
incident?"

> Let the team discuss. If they suggest additions, note them on the shared screen.
> **On your shared screen:** Fill in the recovery verification section (leave
> checkboxes empty, tick them as the team agrees to each point).

---

## STEP 3: SCENARIO B — KEY VAULT SECURITY ALERT (Minute 20:00 to 35:00)

> This is the "Security" scenario. An unauthorized person is trying to steal
> passwords from our digital vault (Azure Key Vault).

**Say this:**

"Now let us switch to Scenario B. This is our security track, which tests
our response to an unauthorized credential compromise.

At 10:25 UTC, Microsoft Defender for Cloud sends us a security alert:

'Key Vault helios-dm0-kv has detected 18 unauthorized attempts to retrieve
a secret (HTTP 403 Forbidden errors) within 5 minutes. These attempts came
from an unknown external IP address: 198.51.100.44. The targeted secret is
called redis-cache-connection.'

This is serious because someone from outside our network is repeatedly trying
to access one of our stored passwords.

**Step 1 — Who responded to the alert?**

I acknowledge in Slack at 10:27 UTC. Because this involves credentials and
unauthorized access to our Key Vault, our procedure requires an immediate
joint escalation to both Constantin and Maurice."

> **On your shared screen:** Fill in the Scenario B section:
> - Who responded: Dipak Singh, 10:27 UTC
> - Escalated to: Constantin and Maurice immediately

"**Step 2 — Creating the incident record.**

At 10:29 UTC, we create ticket AB#34978 in Azure DevOps, tagged as 'security',
'sev-1' (Critical), and 'soc2-cc7'. This is tagged as Severity 1 because any
unauthorized credential access is automatically Critical under our procedure.

**Step 3 — Containment actions (stopping the attacker).**

We take three actions at the same time:

1. Maurice — You check Microsoft Entra ID (our identity management system)
   and immediately disable and delete the compromised Service Principal
   credential. This cuts off the attacker's access.

2. I update the Key Vault firewall rules to restrict access so that only our
   trusted internal networks (VNets) can reach it. No more public internet
   access.

3. I rotate (change) the redis-cache-connection secret — basically generating
   a new password — and then restart the services that depend on it so they
   pick up the new password."

> Pause and ask Maurice:

"Maurice, before we revoke the Service Principal, in a real incident, what
would you check first to make sure we do not accidentally break a legitimate
service that is using the same credential?"

> Let Maurice answer. The expected answer is: check Entra ID sign-in logs to
> see which services are using this Service Principal.

"**Step 4 — How do we verify recovery for this security incident?**

1. **Audit log check:** We run a query in our logs that confirms zero
   requests from the attacker's IP address (198.51.100.44) over the last
   30 minutes. If the count is zero, the attacker is locked out.
2. **Service health check:** Our legitimate backend services
   (ontology-event-processor, cost-ingestion) successfully connect and
   retrieve the new secret using their Managed Identity. They get HTTP 200 OK.
3. **Defender status:** Microsoft Defender for Cloud marks the incident as
   'Mitigated — Credential Rotated and Network Isolated.'
4. **Security sign-off:** Constantin reviews the audit log evidence and
   officially signs off on closing the incident.

Constantin, as the security escalation lead, is there anything else you would
want to see before signing off on closure?"

> Let Constantin answer. He may bring up topics like:
> - Checking if the attacker hit other resources (see Q&A cheat sheet)
> - Whether we need to notify the CISO
> - Whether we need to check threat intelligence feeds for the IP
>
> **Note any additional points on your shared screen.**

---

## STEP 4: POST-INCIDENT REVIEW AND ACTION ITEMS (Minute 35:00 to 55:00)

> This section is where we reflect on what went well and what needs to improve.
> The term "Post-Incident Review" (sometimes called PIR) means: after the
> incident is over, the team discusses what happened, what worked, and what
> to fix. Think of it as a "lessons learned" discussion.

**Say this:**

"Now let us do our Post-Incident Review. This is the 'lessons learned'
discussion, and it is required for our SOC 2 CC7 audit.

**First, what went well?**

1. Response time was fast — both alerts were acknowledged within 2 minutes.
2. Our escalation rules were clear — there was no confusion about who is
   Primary, who is Backup, and who is Incident Commander.
3. We verified recovery using actual metrics, logs, and health checks —
   instead of just guessing that things were fixed.

Does anyone want to add anything else to the 'what went well' list?"

> Let the team add points. Type them into the shared screen template.

"**Second, what gaps did we find? What do we need to improve?**

Based on today's exercise, I have identified these follow-up action items:

**Action Item 1** (Owner: Constantin):
Document our on-call rotation and escalation contacts in the team Wiki.
Right now, our roles are only written in this procedure document. If someone
new joins the team, they need to find this information easily.

**Action Item 2** (Owner: Dipak):
Create a formal Severity Classification guide. Today we classified the
APIM issue as Severity 2 even though 42 percent of requests were failing.
We need clear, written rules for when something is Severity 1 versus
Severity 2, so there is no guessing during a real incident.

**Action Item 3** (Owner: Aditya):
Embed runbook links and diagnostic query links directly into our Slack
alert messages. Right now, when an alert fires, the responder has to
manually go find the right query. We should include a clickable link
right in the alert card.

**Action Item 4** (Owner: Maurice):
Automate Key Vault secret rotation and set up alerts for any public
network access denials. Today we rotated the secret manually. This
should be automated using Azure Event Grid and Azure Functions.

**Action Item 5** (NEW — Owner: Dipak):
Create a customer and stakeholder communication plan. During a Severity 1
outage, we need a defined process for who notifies the business team,
whether we update a status page, and who contacts affected customers.

Does anyone want to add more items or change any of these?"

> Let the team discuss. Capture any new action items on the shared screen.
> **On your shared screen:** Fill in the Action Items table in the Live Notes.

---

## STEP 5: WRAP-UP AND CLOSING (Minute 55:00 to 60:00)

**Say this:**

"That completes our 60-minute tabletop exercise.

To summarize what we covered today:
- We walked through two simulated incidents: an app gateway failure and a
  Key Vault security breach.
- For both, we documented who responded to the alert, how we created the
  incident record, what actions we took, and how we verified recovery.
- We identified action items to strengthen our procedure.

I will do the following after this call:
1. Post the complete meeting notes and recovery verification evidence
   directly on work item AB#34970 in Azure DevOps.
2. Create the follow-up action item tickets as child tasks under AB#34970.
3. Publish the Post-Incident Review to our ADO Wiki.

All of this will be done today so our audit evidence is 100 percent
complete before the October 30 cutoff.

Thanks everyone for your time! Any final questions?"

> If someone asks you to create the tickets:

**Say this:**

"Yes, I have all the tickets pre-written and ready to go. I will create and
link them under AB#34970 right after this call."

> After the call ends, open the files in the `2_INDIVIDUAL_TICKETS` folder and
> copy-paste each one into Azure DevOps as a Child Task under AB#34970.

---

## QUICK REFERENCE: KEY TERMS (If Someone Asks What Something Means)

| Term | What it means in simple words |
|---|---|
| **APIM** | Azure API Management — the front door of our application that handles all incoming requests. |
| **5xx errors** | Server errors. The "5" means the server (our side) broke, not the user's side. |
| **HTTP 504** | A timeout error. The server waited too long for a response and gave up. |
| **Key Vault** | Azure's digital safe where we store passwords, API keys, and certificates securely. |
| **KQL** | Kusto Query Language — a search language we use to look through our logs and find errors. |
| **Service Principal** | A digital identity (like a username and password) that an application uses to connect to Azure services. |
| **Managed Identity / Workload Identity** | A more secure way for apps to connect to Azure services without storing passwords. Azure manages the credentials automatically. |
| **Entra ID** | Microsoft's identity management system (formerly called Azure Active Directory). Where we manage users, apps, and permissions. |
| **Helm / GitOps** | Tools used to deploy and update our applications. "Rollback" means undoing the last update to go back to a working version. |
| **Sev 1 (Critical)** | The most serious type of incident. Customer-facing outage, data breach, or credential compromise. Must respond within 15 minutes. |
| **Sev 2 (Major)** | Serious but not catastrophic. A component is failing but the whole system is not down. Must respond within 30 minutes. |
| **SOC 2 CC7** | A security compliance standard. CC7 specifically covers how a company detects, responds to, and recovers from incidents. |
| **Post-Incident Review (PIR)** | A "lessons learned" meeting held after an incident is resolved. We discuss what happened, what worked, and what to improve. |
| **VNet** | Virtual Network — our private, isolated network inside Azure. Only trusted services inside this network can talk to each other. |
| **NSG** | Network Security Group — firewall rules that control what traffic is allowed in and out of our Azure resources. |
