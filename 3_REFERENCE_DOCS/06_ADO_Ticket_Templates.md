# 06 — Azure DevOps Work Item Templates (Copy & Paste)

These pre-formatted templates can be copied and pasted directly into Azure DevOps if the team decides to create the individual incident simulation tasks and PIR action item tickets under **AB#34970**.

---

## 📋 Quick Creation Steps in Azure DevOps UI

1. Open parent story **AB#34970**.
2. Under **Related Work** (right sidebar), click **Add link** $\rightarrow$ **New item**.
3. Choose:
   * **Link type:** `Child`
   * **Work item type:** `Task`
4. Copy and paste the **Title**, **Assigned To**, **Tags**, and **Description** from the templates below.
5. Click **Save & Close**.

---

# PART 1: Incident Simulation Tickets

---

### Ticket 1: Track A (Reliability) — APIM Gateway 5xx Surge

**Work Item Type:** `Task`  
**Parent Work Item:** `AB#34970`  
**Title:**
```text
[INCIDENT SIMULATION] APIM Gateway 5xx Failure Rate Surge on helios-dm0-apim (Track A)
```

**Assigned To:** `Dipak Singh`  
**Area Path:** `Helios\QRE`  
**Iteration:** Current Sprint  
**Tags:**
```text
incident, sev-2, tabletop, soc2-cc7, reliability
```

**Description (Copy & Paste into Description box):**
```html
<p><strong>Tabletop Incident Simulation — Track A (Reliability)</strong></p>
<p>Fulfills SOC 2 Type II CC7.1 / CC7.2 incident response evidence under parent work item <strong>AB#34970</strong>.</p>

<hr/>
<p><strong>1. Incident Details:</strong></p>
<ul>
  <li><strong>Impacted Resource:</strong> <code>helios-dm0-apim</code> (Resource Group: <code>helios-dm0-us-west3-rg</code>)</li>
  <li><strong>Trigger Alert:</strong> <code>helios-dm0-gateway-failure-rate</code> (42% failure rate observed over 15m window, threshold &gt; 10%)</li>
  <li><strong>Severity:</strong> Sev 2 (Major Service Degradation)</li>
  <li><strong>Notification Route:</strong> Action Group <code>ag-helios-dm0-ops</code> &rarr; Slack <code>#alerts-dm0-ops</code></li>
</ul>

<hr/>
<p><strong>2. Lifecycle Execution:</strong></p>
<ul>
  <li><strong>Who Acked:</strong> Acknowledged by <strong>Dipak Singh</strong> in Slack at 10:20 UTC (within 2 minutes of detection). Escalation backup: Maurice Kennedy.</li>
  <li><strong>Incident Commander:</strong> Constantin Pricochi</li>
  <li><strong>Diagnostic Investigation:</strong> Aditya Mehta executed KQL query in Log Analytics (<code>helios-dm0-logs</code>) isolating errors to upstream HTTP 504 gateway timeouts on container <code>ontology-event-processor</code>.</li>
  <li><strong>Mitigation:</strong> Rollback authorized by Constantin Pricochi; Maurice Kennedy executed container rollback in GitOps.</li>
</ul>

<hr/>
<p><strong>3. Recovery Verification (4-Point Technical Checklist):</strong></p>
<ul>
  <li>[x] <strong>Metric Baseline:</strong> APIM 5xx error rate dropped from 42% to 0.08% and remained stable for &gt; 15 consecutive minutes.</li>
  <li>[x] <strong>Synthetic Health Probe:</strong> <code>GET /ontology-event-processor/health</code> returned HTTP 200 OK in 48ms.</li>
  <li>[x] <strong>Diagnostic Log Audit:</strong> Log Analytics confirmed 0 gateway 504 errors in subsequent 10-minute window.</li>
  <li>[x] <strong>Sign-off:</strong> Dipak Singh confirmed recovery evidence in Slack and resolved incident record.</li>
</ul>
```

---

### Ticket 2: Track B (Security) — Key Vault Unauthorized Access Spike

**Work Item Type:** `Task`  
**Parent Work Item:** `AB#34970`  
**Title:**
```text
[INCIDENT SIMULATION] Key Vault Unauthorized Access Spike on helios-dm0-kv (Track B)
```

**Assigned To:** `Dipak Singh`  
**Area Path:** `Helios\QRE`  
**Iteration:** Current Sprint  
**Tags:**
```text
incident, sev-1, security, tabletop, soc2-cc7
```

**Description (Copy & Paste into Description box):**
```html
<p><strong>Tabletop Incident Simulation — Track B (Security)</strong></p>
<p>Fulfills SOC 2 Type II CC7.3 / CC7.4 security incident response evidence under parent work item <strong>AB#34970</strong>.</p>

<hr/>
<p><strong>1. Incident Details:</strong></p>
<ul>
  <li><strong>Impacted Resource:</strong> <code>helios-dm0-kv</code> (Azure Key Vault in <code>helios-dm0-us-west3-rg</code>)</li>
  <li><strong>Trigger Alert:</strong> Microsoft Defender for Cloud &mdash; 18 unauthorized secret retrieval attempts (HTTP 403 Forbidden) within 5 minutes from untrusted external IP <code>198.51.100.44</code> targeting secret <code>redis-cache-connection</code>.</li>
  <li><strong>Severity:</strong> Sev 1 (Critical Security / Credential Compromise)</li>
  <li><strong>Notification Route:</strong> Action Group <code>ag-helios-dm0-ops</code> &rarr; Slack <code>#alerts-dm0-ops</code></li>
</ul>

<hr/>
<p><strong>2. Lifecycle Execution:</strong></p>
<ul>
  <li><strong>Who Acked:</strong> Acknowledged by <strong>Dipak Singh</strong> in Slack at 10:27 UTC (within 2 minutes).</li>
  <li><strong>Joint Escalation:</strong> Immediately escalated to <strong>Constantin Pricochi</strong> and <strong>Maurice Kennedy</strong> per security escalation protocol.</li>
  <li><strong>Containment Actions:</strong>
    <ol>
      <li>Maurice Kennedy investigated Microsoft Entra ID and immediately revoked/deleted the compromised Service Principal credential.</li>
      <li>Dipak Singh updated Key Vault network firewall rules to enforce trusted Azure VNets and private endpoints only.</li>
      <li>Dipak Singh rotated the <code>redis-cache-connection</code> secret and restarted dependent workloads with the updated secret version.</li>
    </ol>
  </li>
</ul>

<hr/>
<p><strong>3. Recovery Verification (4-Point Technical Checklist):</strong></p>
<ul>
  <li>[x] <strong>Audit Log Zero:</strong> KQL query in <code>helios-dm0-logs</code> confirmed 0 requests from offending IP <code>198.51.100.44</code> post-containment over 30 minutes.</li>
  <li>[x] <strong>Workload Health:</strong> Backend workloads (<code>ontology-event-processor</code>, <code>cost-ingestion</code>) verified authenticating and retrieving new secret via Workload Identity with HTTP 200 OK.</li>
  <li>[x] <strong>Defender Incident Status:</strong> Marked <em>Mitigated &mdash; Credential Rotated &amp; Network Isolated</em>.</li>
  <li>[x] <strong>Security Sign-off:</strong> Constantin Pricochi reviewed audit log verification and signed off on incident closure.</li>
</ul>
```

---

# PART 2: Post-Incident Review (PIR) Action Items

---

### Ticket 3: Action Item 1 — Formalize On-Call Roster in Wiki

**Work Item Type:** `Task`  
**Parent Work Item:** `AB#34970`  
**Title:**
```text
[SOC 2 CC7 Action Item] Document SRE on-call rotation, backup rules, and escalation SLAs in Wiki
```

**Assigned To:** `Constantin Pricochi`  
**Area Path:** `Helios\QRE`  
**Iteration:** Sprint 2  
**Tags:**
```text
soc2-cc7, on-call, sop, table-top-pir
```

**Description (Copy & Paste into Description box):**
```html
<p><strong>Action Item resulting from SRE Tabletop Exercise (AB#34970):</strong></p>
<p>Formalize the team's interim on-call rotation and escalation paths in the Helios Engineering Wiki ahead of automated PagerDuty rollout.</p>

<p><strong>Deliverables:</strong></p>
<ul>
  <li>Publish explicit Primary Responder and Backup Responder weekly schedule.</li>
  <li>Document 15-minute (Sev 1) and 30-minute (Sev 2) missed-ack escalation triggers.</li>
  <li>Provide emergency phone numbers and escalation hierarchy for Incident Commander and Security escalation.</li>
</ul>
```

---

### Ticket 4: Action Item 2 — Publish Severity Classification Rubric

**Work Item Type:** `Task`  
**Parent Work Item:** `AB#34970`  
**Title:**
```text
[SOC 2 CC7 Action Item] Publish formal Incident Severity Classification Rubric with Sev 1/Sev 2 thresholds
```

**Assigned To:** `Dipak Singh`  
**Area Path:** `Helios\QRE`  
**Iteration:** Sprint 2  
**Tags:**
```text
soc2-cc7, sop, incident-management, table-top-pir
```

**Description (Copy & Paste into Description box):**
```html
<p><strong>Action Item resulting from SRE Tabletop Exercise (AB#34970):</strong></p>
<p>Publish an unambiguous Incident Severity Classification Rubric in the repository and team Wiki to eliminate subjective severity assignment during triage.</p>

<p><strong>Deliverables:</strong></p>
<ul>
  <li>Define explicit quantitative thresholds for Sev 1, Sev 2, and Sev 3 (e.g. APIM 5xx &gt; 10%, pipeline failure &gt; 50%, unauthorized Key Vault calls &ge; 5).</li>
  <li>Document SLA targets for acknowledgement, executive updates, and PIR completion (5 business days).</li>
  <li>Include criteria for security incidents requiring immediate joint escalation.</li>
</ul>
```

---

### Ticket 5: Action Item 3 — Embed Runbook Links in Alert Cards

**Work Item Type:** `Task`  
**Parent Work Item:** `AB#34970`  
**Title:**
```text
[SOC 2 CC7 Action Item] Embed runbook and KQL deep links directly into Azure Monitor Slack alert cards
```

**Assigned To:** `Aditya Mehta`  
**Area Path:** `Helios\QRE`  
**Iteration:** Sprint 2  
**Tags:**
```text
soc2-cc7, monitoring, alerts, slack, table-top-pir
```

**Description (Copy & Paste into Description box):**
```html
<p><strong>Action Item resulting from SRE Tabletop Exercise (AB#34970):</strong></p>
<p>Update Azure Monitor alert notification logic and Terraform stack templates to embed actionable runbook and Log Analytics deep links in Slack notifications.</p>

<p><strong>Deliverables:</strong></p>
<ul>
  <li>Update <code>terraform/stacks/monitoring/</code> alert payloads to include direct runbook URLs.</li>
  <li>Include pre-populated KQL query links into <code>helios-dm0-alert-to-slack</code> Logic App alert card.</li>
  <li>Ensure on-call responders can initiate root-cause investigation within 1 click from Slack.</li>
</ul>
```

---

### Ticket 6: Action Item 4 — Automated Key Vault Rotation & Alerting

**Work Item Type:** `Task`  
**Parent Work Item:** `AB#34970`  
**Title:**
```text
[SOC 2 CC7 Action Item] Implement automated Key Vault credential rotation and network deny alerts
```

**Assigned To:** `Maurice Kennedy`  
**Area Path:** `Helios\QRE`  
**Iteration:** Sprint 2  
**Tags:**
```text
soc2-cc7, security, key-vault, automation, table-top-pir
```

**Description (Copy & Paste into Description box):**
```html
<p><strong>Action Item resulting from SRE Tabletop Exercise (AB#34970):</strong></p>
<p>Automate secret rotation workflows and strengthen alert telemetry around unauthorized Key Vault access attempts.</p>

<p><strong>Deliverables:</strong></p>
<ul>
  <li>Deploy automated Event Grid + Azure Function rotation pattern for cache and database credentials.</li>
  <li>Create metric alert in <code>helios-dm0-us-west3-rg</code> triggering whenever Key Vault public network deny count &gt; 0.</li>
  <li>Audit all Service Principals with Key Vault permissions and migrate to Azure Managed Identity / Workload Identity.</li>
</ul>
```
