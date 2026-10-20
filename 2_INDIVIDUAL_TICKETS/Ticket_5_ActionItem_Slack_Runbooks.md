PARENT STORY: AB#34970 (Link type: Child | Work item type: Task)
AREA PATH: Helios\QRE

-----------------------
TITLE:
================================================================================
[SOC 2 CC7 Action Item] Embed runbook and KQL deep links directly into Azure Monitor Slack alert cards

------------------------
ASSIGNED TO:
================================================================================
Aditya Mehta

------------------------
TAGS:
================================================================================
soc2-cc7, monitoring, alerts, slack, table-top-pir

------------------------
DESCRIPTION (Copy all lines below):
================================================================================
Action Item resulting from SRE Tabletop Exercise (AB#34970):
Update Azure Monitor alert notification logic and Terraform stack templates to embed actionable runbook and Log Analytics deep links in Slack notifications.

Deliverables:

- Update terraform/stacks/monitoring/ alert payloads to include direct runbook URLs.
- Include pre-populated KQL query links into helios-dm0-alert-to-slack Logic App alert card.
- Ensure on-call responders can initiate root-cause investigation within 1 click from Slack.
