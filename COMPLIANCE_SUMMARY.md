# Project 7 — Compliance Mapping, Gap Analysis & Roadmap

**Author:** Abhinav Prajapati · **Date:** 2026-11-09 · **Classification:** Confidential

Maps **all 12 confirmed findings from Projects 1–6** (web, AI/LLM, MCP and
DevSecOps layers) to OWASP ASVS L2, NIST CSF and the EU AI Act.

## How to build the 3-tab Google Sheet (Deliverable 1)
Fastest path: **File → Import → Upload `compliance_workbook.xlsx`** — it already
contains four tabs (*OWASP ASVS*, *NIST CSF*, *EU AI Act*, *Traffic-Light
Summary*) with the Status/traffic-light cells colour-coded. Or import each CSV
into its own tab (`compliance_asvs.csv`, `compliance_nist_csf.csv`,
`compliance_eu_ai_act.csv`, `compliance_traffic_light.csv`).

Pass rate per tab = (assessed controls − distinct failed controls) / assessed controls.

## Findings in scope (12, from Projects 1–6)
| ID | Finding | Source | Layer | CVSS | Risk |
|----|---------|--------|-------|------|------|
| F-01 | SQL Injection (DVWA) | P6 | Web | 9.8 | Critical |
| F-02 | Auth bypass via SQLi (MD5 creds) | P6 | Web | 9.8 | Critical |
| F-03 | Secrets embedded in AI system prompt | P1/P6 | AI | 9.1 | Critical |
| F-04 | Prompt injection → system-prompt disclosure | P1/P3/P6 | AI | 8.2 | High |
| F-05 | Flask debug mode enabled | P1/P6 | Web/AI | 7.5 | High |
| F-06 | Reflected XSS (DVWA) | P6 | Web | 6.1 | Medium |
| F-07 | Missing security headers / HttpOnly | P6 | Web | 5.3 | Medium |
| F-08 | MCP arbitrary command execution | P4 | MCP | 9.1 | Critical |
| F-09 | MCP path traversal (read_file) | P4 | MCP | 8.6 | High |
| F-10 | MCP resource poisoning / capability chaining | P4 | MCP | 7.4 | High |
| F-11 | CI/CD pipeline without security gates | P5 | DevSecOps | 8.1 | High |
| F-12 | Vulnerable / unpinned dependencies | P5 | DevSecOps | 7.5 | High |

## Computed pass rates (Deliverable 1)
The "assessed controls" denominator is the in-scope control set evaluated in this
engagement (stated explicitly so the maths is transparent).

| Framework | Distinct failed controls | Assessed (scope) | Pass rate |
|-----------|--------------------------|------------------|-----------|
| OWASP ASVS L2 | 11 — V2.4.1, V5.1.3, V5.3.3, V5.3.4, V5.3.8, V6.4.1, V12.3.1, V14.1.1, V14.2.1, V14.3.2, V14.4.3 | 25 | **56%** |
| NIST CSF | 6 — PR.AC-1, PR.AC-4, PR.DS-5, PR.IP-1, PR.IP-2, ID.RA-1 | 15 | **60%** |
| EU AI Act | 3 — Article 9, Article 10, Article 15 | 9 | **67%** |

Every one of the 12 findings maps to a **failed control in all three frameworks
simultaneously**, so all 12 are cross-framework failures; the 5 highest-priority
are in the gap analysis below.

## Traffic-light compliance summary (Deliverable 2)
| Framework | Overall status | Single most important reason |
|-----------|----------------|------------------------------|
| OWASP ASVS Level 2 | 🔴 Red | Input validation (V5) fails across SQLi, XSS, prompt injection, OS command injection and path traversal; secret management (V6.4) also fails. |
| NIST CSF | 🔴 Red | The **Protect** function is broadly failing — credential management (PR.AC), secure configuration (PR.IP) and data-leak protection (PR.DS). |
| EU AI Act | 🟠 Amber | Article 15 (robustness & cybersecurity) is unmet across the web/AI/MCP layers, but the governance gaps (Art 9/10) are process-addressable and the system is a pre-deployment local lab. |

## Top 5 cross-framework gaps (Deliverable 3)
Full paragraphs are in [`GAP_ANALYSIS.md`](GAP_ANALYSIS.md). Summary:

| # | Gap (findings) | ASVS | NIST CSF | EU AI Act | Risk | Effort |
|---|----------------|------|----------|-----------|------|--------|
| 1 | Hardcoded secrets in AI system prompt (F-03) | V6.4.1 | PR.AC-1 | Art 10/15 | Critical | Easy |
| 2 | SQL injection + auth bypass (F-01, F-02) | V5.3.4 / V2.4.1 | PR.DS-5 / PR.AC-1 | Art 15 | Critical | Medium |
| 3 | MCP RCE + path traversal (F-08, F-09) | V5.3.8 / V12.3.1 | PR.AC-4 / PR.DS-5 | Art 15 | Critical | Hard |
| 4 | CI/CD without gates + vulnerable deps (F-11, F-12) | V14.1.1 / V14.2.1 | PR.IP-2 / ID.RA-1 | Art 9 | High | Medium |
| 5 | Prompt injection / no input validation (F-04, F-10) | V5.1.3 | PR.DS-5 / PR.AC-4 | Art 15 | High | Medium |

## 90-day remediation roadmap (Deliverable 4)
Full table with owners, tools and target dates in
[`REMEDIATION_ROADMAP.md`](REMEDIATION_ROADMAP.md). Phases: **Weeks 1–2**
(Critical + easy wins: secrets, debug, headers, MCP path allow-list) → **Month 1**
(High: parameterised SQL + strong hashing, remove/sandbox MCP command exec, AI
input-validation + output scanner, enable CI/CD gates, pin+patch deps) →
**Months 2–3** (Medium + architectural: output encoding + CSP, MCP capability
redesign + least privilege, monitoring/logging, regression-proofing via gates).
