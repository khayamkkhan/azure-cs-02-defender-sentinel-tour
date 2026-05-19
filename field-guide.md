# Field Guide — Microsoft Security Portal Map

> A practical map of every Microsoft security portal touched in SC-900.
> Each entry: what it covers · URL · screenshot · 1–3 sentences on real-world use.
>
> Created during Project 02 (Defender + Sentinel Tour). Living reference for future projects (P04–P09).

---

## Portal Index

| # | Portal | URL | Status | Screenshot |
| - | ------ | --- | ------ | ---------- |
| 1 | Microsoft Defender XDR | [security.microsoft.com](https://security.microsoft.com) | ✅ Covered | `22-defender-xdr-portal.png` |
| 2 | Defender for Cloud | [portal.azure.com → Defender for Cloud](https://portal.azure.com) | ✅ Covered | `23-defender-for-cloud-portal.png` |
| 3 | Microsoft Sentinel | [security.microsoft.com → Sentinel](https://security.microsoft.com) | ✅ Covered | `21-sentinel-overview-defender-portal.png` |
| 4 | Microsoft Entra admin | [entra.microsoft.com](https://entra.microsoft.com) | ✅ Covered | `24-entra-admin-center.png` |
| 5 | Microsoft Purview | [purview.microsoft.com](https://purview.microsoft.com) | ⛔ Deferred to Project 07 | — |
| 6 | Intune admin | [intune.microsoft.com](https://intune.microsoft.com) | ⛔ Out of scope | — |
| 7 | Microsoft 365 Defender (alias) | [security.microsoft.com](https://security.microsoft.com) | ℹ️ Same portal as #1 | (covered by #1) |
| 8 | Compliance portal | [compliance.microsoft.com](https://compliance.microsoft.com) | ⛔ Deferred (license-gated) | — |

---

## 1. Microsoft Defender XDR

**URL:** https://security.microsoft.com
**Screenshot:** `screenshots/22-defender-xdr-portal.png`
**Status:** ✅ Covered

**What it covers:**
The unified extended detection and response (XDR) portal — Microsoft's single pane of glass for security across endpoints (Defender for Endpoint), identities (Defender for Identity), email and collaboration (Defender for Office 365), and cloud apps (Defender for Cloud Apps). As of 2025–2026 Microsoft has merged the Microsoft Sentinel SIEM experience into this same portal, creating a "unified security operations platform" where SIEM and XDR alerts share one incident queue.

**Observed in this lab:** Sentinel onboarding banner ("Microsoft Sentinel is here"), SOC Optimization scorecard (13/14 optimization opportunities), 2 active data connectors visible, MITRE ATT&CK navigator, behavior analytics (UEBA) toggle, and threat intelligence panel.

**SOC angle:** This is the day-job portal for a Cloud SOC analyst working at a Microsoft-centric shop. Most investigation, hunting, and incident triage happens here rather than in the Azure portal.

---

## 2. Defender for Cloud

**URL:** https://portal.azure.com → search "Microsoft Defender for Cloud"
**Screenshot:** `screenshots/23-defender-for-cloud-portal.png`
**Status:** ✅ Covered

**What it covers:**
Cloud-native application protection platform (CNAPP) — combines Cloud Security Posture Management (CSPM) with Cloud Workload Protection (CWPP) for Azure (and via plans, AWS/GCP). Provides Secure Score (a single posture metric), regulatory compliance dashboards (MCSB by default; CIS, NIST, PCI-DSS, ISO available with Defender CSPM), attack path analysis, and resource-level recommendations.

**Observed in this lab:** Day 0 Secure Score of 100% (still calibrating — only 1 resource assessed), 0 attack paths, 1 active recommendation, Microsoft Cloud Security Benchmark visible under Regulatory compliance. Defender plans are all set to **Off** except Foundational CSPM (free tier) to control cost.

**SOC angle:** Foundational CSPM = free baseline. The paid Defender plans (Servers, Storage, Databases, etc.) add agentless vulnerability scanning, anti-malware, and workload-specific threat detection — turn on selectively per workload.

---

## 3. Microsoft Sentinel

**URL:** https://security.microsoft.com → Microsoft Sentinel (Defender portal)
**Backing workspace:** Log Analytics workspace `log-portfolio-baseline` (East US)
**Screenshot:** `screenshots/21-sentinel-overview-defender-portal.png`
**Status:** ✅ Covered

**What it covers:**
Microsoft's cloud-native SIEM + SOAR — collects logs from any source (Azure, on-prem, third-party clouds), runs detection analytics (scheduled, NRT, ML-based), generates incidents, supports KQL-based threat hunting, and orchestrates response via Logic Apps playbooks. Now lives inside the unified Defender portal alongside XDR.

**Observed in this lab:** Onboarded to `log-portfolio-baseline`, 2 connectors active (Azure Activity + Microsoft Entra ID), 1 custom analytic rule (TEST — Self-Activity Trigger), 0.5 GB/day ingestion cap enforced, Pay-as-you-go pricing tier.

**SOC angle:** This is *the* product for the SC-200 cert and the daily tool for a Cloud SOC analyst. KQL skill from Project 02 onward maps directly to SC-200 detection writing and to threat-hunting work in Projects 05/06.

**Cost discipline applied:** Daily cap = 0.5 GB. Without this, a single misfired query or chatty connector can run $30+ in an afternoon. Verified in Log Analytics → Usage and estimated costs → Daily cap.

---

## 4. Microsoft Entra admin center

**URL:** https://entra.microsoft.com
**Screenshot:** `screenshots/24-entra-admin-center.png`
**Status:** ✅ Covered

**What it covers:**
The dedicated admin portal for Microsoft Entra (formerly Azure AD) identity services — user/group management, application registrations, Conditional Access policies, MFA, SSPR, Privileged Identity Management (PIM), Identity Protection (P2), Access Reviews, Entitlement Management, and hybrid identity (Entra Connect). Separated from the broader Azure portal in 2023 to give identity admins a focused surface.

**Observed in this lab:** Default Directory tenant (free tier), **Identity Secure Score 73.66%**, account holds Global Administrator role assignment, Microsoft Entra Connect = Disabled (no hybrid identity), Entra Free license plan.

**SOC angle:** Identity Secure Score is the metric to watch — every improvement (enforce MFA on all admins, block legacy auth, enable Conditional Access) directly maps to recommendations here. SC-300 covers this portal in depth; SC-900 expects vocabulary recognition.

---

## 5. Microsoft Purview portal

**URL:** https://purview.microsoft.com
**Status:** ⛔ Out of scope for Project 02 — covered in Project 07 (paired with SC-401: Information Security Administrator)

**Reason for deferral:**
Full Purview management features (Information Protection, DLP, Insider Risk Management, eDiscovery, Compliance Manager) require **Microsoft 365 E5**, **Microsoft 365 E5 Compliance**, or a standalone Microsoft Purview license. The portfolio's free-tier tenant does not include these, so the portal lands on a marketing/setup page rather than functional management surfaces. Attempting to load `purview.microsoft.com` in this lab returned a cross-tenant auth error (`Microsoft Services` tenant routing) — a real-world signal of the licensing gap.

**What Purview covers (for SC-900 vocabulary):**
Microsoft's unified data governance, security, and compliance platform — data classification (built-in + custom sensitive info types + trainable classifiers), sensitivity labels, data loss prevention (DLP), insider risk management, data lifecycle management (retention/deletion), records management, eDiscovery (Standard + Premium), audit logging, compliance manager, and data map / unified catalog for cross-source data discovery.

**Re-evaluation milestone:** Project 07 build (SC-401 study window) — at minimum, a Microsoft 365 E5 trial will be activated then to demo Purview Information Protection on the persistent Contoso WebApp workload.

---

## 6. Microsoft Intune admin center

**URL:** https://intune.microsoft.com
**Status:** ⛔ Out of scope for Project 02 — not on the Microsoft Cybersecurity Architect roadmap projects

**What Intune covers (for SC-900 vocabulary):**
Microsoft's unified endpoint management (UEM) platform — device enrollment (Windows, macOS, iOS, Android, Linux), configuration profiles, compliance policies, application deployment, app protection policies (MAM without enrollment), security baselines, and remote actions (wipe, retire, lock). Integrates with Microsoft Entra Conditional Access to enforce "only compliant devices can sign in." Sometimes referred to as Microsoft Endpoint Manager (legacy term).

**Reason for deferral:**
None of the 9 portfolio projects center on endpoint management. SC-900 only requires recognition of the Intune admin center as the place where device/app management lives (vs Defender = threat detection, Purview = data governance, Entra = identity). The Microsoft Cybersecurity Architect path covers Intune contextually inside AZ-500 / SC-100 architectural discussions, but does not require a hands-on Intune build.

---

## 7. Microsoft 365 Defender (alias)

**URL:** https://security.microsoft.com
**Status:** ℹ️ Identical portal to #1 (Microsoft Defender XDR) — no separate screenshot

**Naming clarification:**
Microsoft has been renaming this portal across iterations:
- "Microsoft 365 Defender" — original (~2020) email-and-endpoint focused name
- "Microsoft Defender XDR" — current preferred name as of 2024–2026
- Both URLs resolve to the same portal at `security.microsoft.com`

For SC-900 exam: treat both names as equivalent. If a question references "Microsoft 365 Defender portal," it's the same surface as "Microsoft Defender XDR portal."

---

## 8. Compliance portal (Microsoft Purview compliance portal)

**URL:** https://compliance.microsoft.com
**Status:** ⛔ Deferred — same licensing gates as Microsoft Purview portal (#5)

**What it covers (for SC-900 vocabulary):**
The legacy compliance management surface that hosted DLP, retention, eDiscovery, audit, communication compliance, and compliance manager prior to consolidation under the Microsoft Purview portal. As of 2025–2026, Microsoft is unifying compliance.microsoft.com into purview.microsoft.com — both currently coexist but Microsoft directs new users to Purview portal. Many SC-900 study guides still reference compliance.microsoft.com because it's been the documented surface for longest.

**Re-evaluation milestone:** Same as #5 — Project 07 / SC-401 study window with M365 E5 trial.

---

## SC-900 Field-Guide Coverage Score

| Coverage | Count | Of total | % |
| -------- | ----- | -------- | - |
| ✅ Covered with screenshot | 4 | 8 | 50% |
| ℹ️ Documented as alias (no separate shot) | 1 | 8 | 12.5% |
| ⛔ Deferred with documented justification | 3 | 8 | 37.5% |

**Honest scoping note:** Three portals are intentionally deferred to later projects rather than padded with marketing-page screenshots. This reflects the portfolio's "scope intentionally, document gaps" principle from `feedback_portfolio_project_standards.md` — recruiters notice the difference between thorough vs comprehensive.
