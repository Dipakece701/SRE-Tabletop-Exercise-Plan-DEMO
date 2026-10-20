================================================================================
SCREEN 2: STEP-BY-STEP FACILITATOR SCRIPT (KEEP THIS OPEN ON 2ND SCREEN)
================================================================================
Meeting: SRE Tabletop Exercise (AB#34970 / SOC 2 CC7)
Duration: 60 Minutes
Attendees: Constantin Pricochi, Maurice Kennedy, Aditya Mehta, Dipak Singh

--------------------------------------------------------------------------------
BEFORE THE MEETING (5 Minutes Before)
--------------------------------------------------------------------------------
1. SCREEN 1 (Your Main Screen):
   - Keep Teams/Zoom window here (DO NOT share screen).
   - Have your browser open to parent work item:
     https://dev.azure.com/qcellsces/Helios/_workitems/edit/34970
   - Keep "ALL_TICKETS_READY_TO_COPY.txt" open in Notepad minimized, ready if needed.

2. SCREEN 2 (Your 2nd Monitor):
   - Keep THIS script open full screen. Just read down this page!

================================================================================
STEP 1: OPENING THE MEETING (00:00 - 05:00)
================================================================================
[Wait 1-2 minutes for everyone to join, then read aloud]:

"Hi everyone, thanks for joining on time. Today we are conducting our 60-minute 
SRE Tabletop Exercise for work item AB#34970 to provide evidence for our 
SOC 2 CC7 audit cutoff on October 30.

The goal today is to walk through our newly drafted Incident Response Procedure v1.0 
on DEMO (dm0) and test our response from detection all the way to recovery verification. 
This is purely a discussion exercise—no live changes will be made to any systems.

Under our interim procedure, our team roster for today's simulation is:

- Primary Responder: Dipak Singh
- Backup Responder: Maurice Kennedy
- Incident Commander: Constantin Pricochi
- Security Escalation: Constantin Pricochi and Maurice Kennedy

I'll facilitate the discussion and capture our notes and PIR write-up in the 
background. We will walk through two quick scenarios: 
1. An APIM 5xx gateway failure surge.
2. A Key Vault unauthorized access alert.

Let's begin with Scenario A."

================================================================================
STEP 2: SCENARIO A — APIM 5xx FAILURE SURGE (05:00 - 20:00)
================================================================================
[Read aloud]:

"At 10:18 UTC, an automated alert fires in #alerts-dm0-ops from ag-helios-dm0-ops:
Alert name: 'helios-dm0-gateway-failure-rate'
Symptom: 42% of incoming requests on helios-dm0-apim are failing with HTTP 5xx 
over the last 15 minutes. Our alert threshold is 10%.

Step 1: Who acked?
As Primary Responder, I acknowledge in Slack at 10:20 UTC:
'ACKNOWLEDGED by @Dipak Singh at 10:20 UTC. Opening triage bridge. Declaring Sev 2 incident.'
Maurice, per our procedure, if I had not acknowledged within 15 minutes, you as 
backup would have stepped in immediately.

Step 2: Incident Record
At 10:22 UTC, I create work item AB#34974 in Azure DevOps under Helios\QRE tagged 
'incident' and 'sev-2'.

Step 3: Investigation & Mitigation"

[Turn to Aditya and ask]:
"Aditya, you run the KQL diagnostic in Log Analytics (helios-dm0-logs). The query 
reveals that ontology-event-processor is timing out with HTTP 504 errors exceeding 
30 seconds, while plan-narration is healthy."

[Turn to Constantin and ask]:
"Constantin, as Incident Commander, do you authorize restarting the pod or 
rolling back ontology-event-processor to the previous Helm digest?"
(Wait for Constantin to answer: "Yes, approved / rollback")

"Maurice executes the rollback in GitOps."

[Read aloud]:
"Step 4: How do we verify recovery before closing?
Under Procedure v1.0, we execute our 4-point verification checklist:
1. Metric check: APIM 5xx error rate drops from 42% to 0.08% for > 15 minutes.
2. Synthetic probe: GET /ontology-event-processor/health returns HTTP 200 OK.
3. Diagnostic KQL: Log Analytics confirms zero 504 errors in last 10 minutes.
4. I post recovery proof into Slack and resolve the incident record."

================================================================================
STEP 3: SCENARIO B — KEY VAULT SECURITY ALERT (20:00 - 35:00)
================================================================================
[Read aloud]:

"Now let's switch to Scenario B at 10:25 UTC. This is our Security track testing 
unauthorized credential compromise under SOC 2 CC7.

The Alert:
Microsoft Defender for Cloud alerts: Key Vault helios-dm0-kv has detected 18 
unauthorized secret retrieval attempts (HTTP 403 Forbidden) within 5 minutes from 
external IP 198.51.100.44 targeting secret redis-cache-connection.

Step 1: Who acked?
I acknowledge in Slack at 10:27 UTC. Because this involves credentials and 
unauthorized Key Vault access, our procedure mandates immediate joint escalation 
to Constantin and Maurice.

Step 2: Incident Record
At 10:29 UTC, we log AB#34978 in Azure DevOps under Helios\QRE tagged 'security', 
'sev-1', and 'soc2-cc7'.

Step 3: Containment Actions
1. Maurice checks Microsoft Entra ID and immediately disables and deletes the 
   compromised Service Principal credential.
2. Dipak updates Key Vault firewall rules to restrict access to trusted VNets only.
3. Dipak rotates the redis-cache-connection secret and restarts dependent workloads 
   with the new secret version.

Step 4: How do we verify recovery?
1. Audit Log Zero: KQL in helios-dm0-logs confirms 0 requests from IP 198.51.100.44 
   over the last 30 minutes.
2. Workload Health: Services pull the new secret via Workload Identity with HTTP 200.
3. Microsoft Defender marks the incident Mitigated.
4. Constantin reviews the audit log evidence and signs off on closure."

================================================================================
STEP 4: POST-INCIDENT REVIEW & ACTION ITEMS (35:00 - 55:00)
================================================================================
[Read aloud]:

"Now let's do our 15-minute Post-Incident Review (PIR) and capture our action items 
for the SOC 2 audit.

First, what went well?
1. Response time was under 2 minutes for both alerts.
2. Clear escalation rules prevented confusion between primary and backup.
3. Recovery was verified with metrics and KQL instead of guessing.

Second, what gaps did we identify that we need to track for SOC 2 CC7?
We have 4 follow-up action items:
1. Document SRE on-call rotation and escalation matrix in team Wiki (Owner: Constantin).
2. Formalize Incident Severity Classification Rubric with Sev 1/2 thresholds (Owner: Dipak).
3. Embed runbook and KQL deep links directly into Slack alert cards (Owner: Aditya).
4. Automate Key Vault secret rotation and public network deny alerts (Owner: Maurice)."

================================================================================
STEP 5: WRAP-UP & CLOSING (55:00 - 60:00)
================================================================================
[Read aloud]:

"That completes our 60-minute tabletop exercise! 
I will post the complete meeting notes, recovery verifications, and link the 
action items directly on AB#34970 today so our audit evidence is 100% complete 
ahead of the October 30 cutoff.

Thanks everyone for your time!"

================================================================================
IF ANYONE ASKS YOU TO CREATE THE TICKETS IN ADO DURING OR AFTER THE CALL:
================================================================================
Just say:
>"Yes, I have all 6 tickets pre-written in Notepad. I will create and link them under AB#34970 right after this call."
>(Then open "ALL_TICKETS_READY_TO_COPY.txt" on Screen 1 and paste them in!)
================================================================================
