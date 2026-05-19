# Project 02 — Defender + Sentinel Tour Lab

> **Pairs with**: SC-900 Security, Compliance & Identity Fundamentals
> **Phase**: 1 — Foundations
> **Status**: 🔒 Locked until SC-900 study begins
> **Effort**: 2 weekends (~10 hours)
> **Cost**: ~$3–5 if torn down promptly

---

## Why This Project

SC-900 throws the entire Microsoft security ecosystem at you in one exam: Defender for Cloud, Defender XDR (Endpoint, Identity, Office, Cloud Apps), Sentinel, Entra ID Protection, Purview, Priva. It's a vocabulary test masquerading as a fundamentals exam.

The fastest way to pass is to **see the portals in person, once**. This project walks every relevant portal, deploys the smallest possible Sentinel workspace, and produces a "field guide" that maps every product name to (a) what it does (b) which portal it lives in (c) one screenshot.

---

## What You Build

- A Sentinel workspace with 2 free-tier connectors live (Azure Activity, Microsoft Entra)
- Defender for Cloud enabled (Free tier across the subscription)
- A `field-guide.md` mapping each Microsoft security product → portal URL → one-paragraph description → screenshot
- One synthetic incident generated and triaged end-to-end

---

## Prerequisites

- Project 01 complete (baseline tenant + Log Analytics workspace exist)
- SC-900 study underway

---

## Architecture

```
Subscription
├── Defender for Cloud (Free tier)
│   └── Microsoft Cloud Security Benchmark (assessments)
└── rg-portfolio-baseline
    └── log-portfolio-baseline ← Sentinel onboarded here
        ├── Connector: Azure Activity
        ├── Connector: Microsoft Entra ID
        └── Connector: Threat Intelligence (TAXII free feed, optional)
```

---

## Build Steps

### 1. Onboard Sentinel onto the existing workspace

Microsoft Sentinel → Create → select `log-portfolio-baseline` → Add. **Set daily ingestion cap to 0.5 GB** immediately (Workspace settings → Usage and estimated costs → Daily cap). Without this, one bad query can cost $30 in an afternoon.

### 2. Enable Defender for Cloud

Defender for Cloud → Environment settings → subscription → leave all plans on Free for now. Note the secure score baseline — you'll watch it improve across later projects.

### 3. Connect free data sources

Sentinel → Data connectors:
- **Azure Activity** (free) — every control-plane action
- **Microsoft Entra ID** (Sign-in logs require Entra P1 trial, Audit logs are free)
- **Threat Intelligence — TAXII** (optional, AlienVault OTX is free)

Wait 15–30 minutes for first events to flow.

### 4. Verify with KQL

Sentinel → Logs:

```kql
AzureActivity
| take 10
```

If you see rows, the pipeline works. Try:

```kql
AzureActivity
| where OperationNameValue endswith "write"
| summarize count() by Caller, ResourceGroup
| sort by count_ desc
```

### 5. Tour the portals (the actual learning)

Open each, screenshot the landing page, write 3 sentences in `field-guide.md` about what each does:

| Portal | URL | What it covers |
|--------|-----|----------------|
| Microsoft Defender XDR | security.microsoft.com | Unified incidents across Endpoint/Identity/Office/Cloud Apps |
| Defender for Cloud | portal.azure.com → Defender for Cloud | Posture + workload protection on Azure resources |
| Microsoft Sentinel | portal.azure.com → Sentinel | SIEM/SOAR — log analytics + detection + response |
| Microsoft Entra admin | entra.microsoft.com | Identity management |
| Microsoft Purview | purview.microsoft.com | Data governance + DLP + sensitivity labels |
| Intune admin | intune.microsoft.com | Device management + compliance |
| Microsoft 365 Defender | security.microsoft.com (alias for XDR) | Email + collaboration security |
| Compliance portal | compliance.microsoft.com | Audit, eDiscovery, retention, communication compliance |

### 6. Generate one synthetic incident

In Sentinel, create the analytic rule **"Sign-in from IP addresses on watchlist"** — even with no watchlist, run it as a *Scheduled* query to fire once on your own sign-in. When it triggers:

1. Open the incident in Sentinel
2. Walk the entities (your account, your IP)
3. Run "Investigation" graph
4. Add a comment + close as Benign — Test
5. Screenshot every step

This is the **first incident in your career as the analyst building the SIEM**, not just consuming it.

### 7. Document the SC-900 product map

`field-guide.md` should now have one section per Microsoft security pillar with screenshots. This becomes your SC-900 quick-reference.

---

## SOC Value / Attack Scenarios

| Attack | MITRE | What you'd see in this lab |
|--------|-------|----------------------------|
| Privilege escalation by adding Owner role | T1098 | `AzureActivity` row — `Microsoft.Authorization/roleAssignments/write` |
| MFA fatigue / push-bombing | T1621 | Entra Sign-in logs → repeated MFA challenges from same IP |
| Resource creation in unexpected region | T1583 | `AzureActivity` resource creates outside `eastus` |

You won't *write* detections yet — that's Project 05 (SC-200). For now, just confirm the telemetry is there.

---

## Deliverables Checklist

- [ ] GitHub repo `azure-cs-02-defender-sentinel-tour`
- [ ] `field-guide.md` with all 8 portals + screenshots + descriptions
- [ ] One synthetic incident screenshotted end-to-end
- [ ] KQL query collection (5+ basic queries) in `kql/` folder
- [ ] Architecture diagram
- [ ] Attack Scenarios section in README
- [ ] LinkedIn post: "SC-900 passed + first Sentinel workspace shipped"

---

## End of Session — What to Keep vs Stop

> [!success] Nothing to stop — Sentinel at minimal ingestion is ~$5/month

| Resource | Action | ~Cost/month |
|----------|--------|-------------|
| Sentinel workspace | ✅ Always keep | ~$5 (minimal connectors) |
| Defender for Cloud (Free tier) | ✅ Always keep | Free |
| Azure Activity connector | ✅ Always keep | Free |
| Microsoft Entra connector | ✅ Always keep | ~$2–3 (ingestion) |
| TAXII threat intel feed | ⛔ Disconnect when not studying | Reduces ingestion |

**Idle cost: ~$5–8/month total (P01+P02)** — verify daily ingestion cap stays at 0.5 GB.

---

## Cleanup

After SC-900 pass:
1. **Keep** Sentinel onboarded — Project 05 expands on this
2. **Keep** Defender for Cloud Free tier
3. **Verify** daily cap still 0.5 GB
4. Disconnect TAXII feed if you connected one

If you decide to pause between SC-900 and AZ-104, offboard Sentinel from the workspace (free) but don't delete the workspace itself.

---

## Resources

- Microsoft Learn: [SC-900 learning path](https://learn.microsoft.com/training/courses/sc-900t00)
- Docs: [Sentinel quickstart](https://learn.microsoft.com/azure/sentinel/quickstart-onboard)
- Cross-link: CCDL2 Module 5 (Threat Hunting / KQL) — same KQL dialect, exact same query language
- Cross-link: `reference_ccdl2_threat_hunting.md`
