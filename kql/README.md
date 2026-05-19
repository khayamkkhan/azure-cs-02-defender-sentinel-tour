# KQL Query Collection — Project 02

Curated KQL queries used to verify the Sentinel pipeline, troubleshoot connector issues, and explore Azure Activity / Entra ID telemetry during the SC-900 lab. Built around the principle of "every query is one I actually ran during the build — not generic copy-paste."

## Query Index

| File | Source Table(s) | Purpose |
| ---- | --------------- | ------- |
| [`01-azure-activity-recent-writes.kql`](./01-azure-activity-recent-writes.kql) | `AzureActivity` | Smoke-test query after Sentinel onboarding — confirms Activity log connector is shipping |
| [`02-table-inventory.kql`](./02-table-inventory.kql) | `union *` | Workspace data inventory. **First query to run when diagnosing missing connectors** — caught the Entra ID first-activation lag |
| [`03-auditlogs-by-activity.kql`](./03-auditlogs-by-activity.kql) | `AuditLogs` | First Entra audit visibility query after connector activation |
| [`04-self-activity-detection.kql`](./04-self-activity-detection.kql) | `AzureActivity` | Backing query for the `TEST — Self-Activity Trigger` analytic rule (Block G end-to-end pipeline validation) |
| [`05-policy-denied-creates.kql`](./05-policy-denied-creates.kql) | `AzureActivity` | Surfaces resources blocked by Azure Policy. Documents the Block B Sentinel-vs-tag-policy troubleshooting story |
| [`06-audit-by-initiated-by.kql`](./06-audit-by-initiated-by.kql) | `AuditLogs` | Distinguish user-initiated vs service-principal-initiated audit events. Foundation for SC-300 / SC-200 work |

---

## Convention

Every `.kql` file includes:

1. A `// ===` header block: **purpose**, **author**, **date**, **source tables**, **expected result count**, **where it was used in the lab**
2. The query body with line-by-line comments for any non-obvious operator
3. A `// Example output` block at the end showing sanitised sample rows from the lab

This makes the queries self-documenting for future-me and recruiters reading the repo.

## How to run

Paste any of the above into:
- **Defender portal** → Microsoft Sentinel → **Logs**
- **Azure portal** → Log Analytics workspace `log-portfolio-baseline` → **Logs**
- Or **Sentinel → Threat Hunting** in the Defender portal (queries 03, 04, 06 are good candidates to convert into hunting queries with `// HuntId: ...` metadata in Project 05)

## Cross-reference

Queries 02 and 05 are the **evidence queries** referenced in Section #12 (Troubleshooting & Lessons Learned) of the project README. They're the "show, don't tell" half of the documented incidents.
