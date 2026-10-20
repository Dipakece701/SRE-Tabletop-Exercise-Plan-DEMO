# 05 — Technical Telemetry & KQL Reference (`dm0`)

This document provides the exact technical queries, resource identifiers, and configurations backing both the Reliability (APIM) and Security (Key Vault) tabletop scenarios.

---

## 1. Cloud Infrastructure Mapping (`dm0`)

* **Subscription:** `Helios - Demo` (`bb47b7ba-e899-4c87-be6e-2f36dc1165c7`)
* **Resource Group:** `helios-dm0-us-west3-rg`
* **APIM Gateway:** `helios-dm0-apim`
* **Key Vault:** `helios-dm0-kv`
* **Log Analytics Workspace:** `helios-dm0-logs` (`/subscriptions/bb47b7ba-e899-4c87-be6e-2f36dc1165c7/resourceGroups/helios-dm0-us-west3-rg/providers/Microsoft.OperationalInsights/workspaces/helios-dm0-logs`)
* **Action Group:** `ag-helios-dm0-ops`
* **Slack Webhook Relay:** Logic App `helios-dm0-alert-to-slack`

---

## 2. Track A: APIM Gateway Failure Rate Alert & KQL

From `terraform/stacks/monitoring/alerts-gateway-failure-rate.tf`:

* **Alert Name:** `helios-dm0-gateway-failure-rate`
* **Severity:** 2 (Sev 2)
* **Evaluation Frequency:** `PT5M` (every 5 minutes)
* **Window Duration:** `PT15M` (15 minutes sliding)
* **Threshold:** Breached when `total >= 20 and todouble(failed) / total > 0.1`

### Alert KQL Query:
```kql
let services = dynamic(["HELIOS-DM0-APIM"]);
let excluded = dynamic({"telemetry-api": "2026-10-23T00:00:00Z"});
AzureDiagnostics
| where TimeGenerated > ago(15m) and Category == "GatewayLogs"
| where Resource in (services)
| extend excluded_until = todatetime(excluded[apiId_s])
| where isnull(excluded_until) or excluded_until <= now()
| summarize total = count(), failed = countif(toint(responseCode_d) >= 500) by apiId_s
| where total >= 20 and todouble(failed) / total > 0.1
```

### Diagnostic Query: Response Code Breakdown by API
```kql
AzureDiagnostics
| where TimeGenerated > ago(1h) and Category == "GatewayLogs"
| where Resource == "HELIOS-DM0-APIM"
| summarize TotalCalls = count(),
            SuccessCalls = countif(toint(responseCode_d) < 400),
            ClientErrors = countif(toint(responseCode_d) between (400 .. 499)),
            ServerErrors = countif(toint(responseCode_d) >= 500),
            ErrorRate = round(100.0 * countif(toint(responseCode_d) >= 500) / count(), 2)
  by apiId_s
| order by ServerErrors desc
```

### Diagnostic Query: Distinguish APIM Policy Errors vs Upstream Backend Errors
```kql
AzureDiagnostics
| where TimeGenerated > ago(1h) and Category == "GatewayLogs"
| where Resource == "HELIOS-DM0-APIM"
| where toint(responseCode_d) >= 500
| project TimeGenerated, apiId_s, operationId_s, 
          GatewayCode = responseCode_d, 
          BackendCode = backendResponseCode_d, 
          BackendTime_ms = backendTime_d, 
          TotalTime_ms = totalTime_d, 
          LastError = lastError_s
| take 25
```

---

## 3. Track B: Key Vault Security Telemetry & KQL

### Detection: Anomalous or Unauthorized Key Vault Access Attempts
```kql
AzureDiagnostics
| where TimeGenerated > ago(1h)
| where ResourceProvider == "MICROSOFT.KEYVAULT" and Resource == "HELIOS-DM0-KV"
| where OperationName in ("SecretGet", "SecretList", "KeyGet", "VaultGet")
| summarize TotalRequests = count(),
            Unauthorized401 = countif(httpStatusCode_d == 401),
            Forbidden403 = countif(httpStatusCode_d == 403),
            Success200 = countif(httpStatusCode_d == 200)
  by CallerIPAddress, identity_claim_appid_g, OperationName
| order by Forbidden403 desc
```

### Forensic Query: Identify Specific Compromised Secrets & Callers
```kql
AzureDiagnostics
| where TimeGenerated > ago(2h)
| where ResourceProvider == "MICROSOFT.KEYVAULT" and Resource == "HELIOS-DM0-KV"
| where httpStatusCode_d in (401, 403)
| project TimeGenerated, OperationName, id_s, CallerIPAddress, clientInfo_s, identity_claim_appid_g, httpStatusCode_d
| order by TimeGenerated desc
```

### Recovery Check Query: Confirm Zero Traffic from Offending IP
```kql
AzureDiagnostics
| where TimeGenerated > ago(30m)
| where ResourceProvider == "MICROSOFT.KEYVAULT" and Resource == "HELIOS-DM0-KV"
| where CallerIPAddress == "198.51.100.44"
| summarize OffendingAttempts = count()
```
*(Recovery verified when `OffendingAttempts == 0` over the last 30 minutes).*

---

## 4. Grafana Dashboard Reference

* **Folder:** `Helios Platform`
* **Dashboard:** `APIM Gateway Performance & Failure Rates`
* **Panels to Reference:**
  1. *Gateway Failure Rate % (Over Time)* — Breached 10% line.
  2. *Status Code Distribution (Stacked Area)* — Surge in red 500/503 bands.
  3. *Latency Breakdown (P95 Total vs P95 Backend)* — P95 backend latency spiked $> 30\text{s}$.
