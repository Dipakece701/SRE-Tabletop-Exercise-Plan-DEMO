PARENT STORY: AB#34970 (Link type: Child | Work item type: Task)
AREA PATH: Helios\QRE

-----------------------
TITLE:
================================================================================
[SOC 2 CC7 Action Item] Implement automated Key Vault credential rotation and network deny alerts

------------------------
ASSIGNED TO:
================================================================================
Maurice Kennedy

------------------------
TAGS:
================================================================================
soc2-cc7, security, key-vault, automation, table-top-pir

-------------------------
DESCRIPTION (Copy all lines below):
================================================================================
Action Item resulting from SRE Tabletop Exercise (AB#34970):
Automate secret rotation workflows and strengthen alert telemetry around unauthorized Key Vault access attempts.

Deliverables:

- Deploy automated Event Grid + Azure Function rotation pattern for cache and database credentials.
- Create metric alert in helios-dm0-us-west3-rg triggering whenever Key Vault public network deny count > 0.
- Audit all Service Principals with Key Vault permissions and migrate to Azure Managed Identity / Workload Identity.
