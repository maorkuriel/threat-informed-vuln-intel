# Guided Walkthrough

!!! abstract "What you will learn"
    This walkthrough follows a security engineer through a complete threat-informed vulnerability intelligence workflow — from a raw scanner finding to a confirmed exploit report, developer-ready fix, and compliance evidence. Each stage can be adopted independently.

---

## The Problem in One Number

An enterprise with **2,847 open vulnerabilities** and a team that can fix **30 per month** faces a **95-month remediation backlog**.

CVSS scores do not help. Research shows **85% of CVEs are never exploited in the wild** — but CVSS rates most of them Critical or High anyway. Organizations burn engineering time on theoretical risks while genuinely exploited vulnerabilities wait in the queue.

Threat-informed vulnerability intelligence replaces CVSS-based guesswork with execution-based proof: does this vulnerability actually work in your codebase, and what real-world attacks does it enable?

---

## The Scenario

**Jordan** is a Security Engineer at a Series B SaaS company. Her Snyk scan just returned 312 findings across 8 microservices. She has three engineers who can work on security remediation this sprint — and a board meeting in two weeks where the CISO needs to report on breach exposure.

She needs to answer three questions:

1. Which of these 312 findings are actually exploitable?
2. What real attacks do they enable?
3. What evidence can she show the auditor?

---

## Setup

```bash
git clone https://github.com/maorkuriel/threat-informed-vuln-intel.git
cd threat-informed-vuln-intel
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Jordan adds her API keys to `.env`:

```
GEMINI_API_KEY=AIza...
NVD_API_KEY=abc123...
GITHUB_TOKEN=ghp_...
```

Docker must be running — the sandbox executor uses ephemeral containers for safe exploit validation.

---

## Stage 1: Vulnerability Intake

Jordan exports her Snyk findings as JSON and feeds them in:

```bash
python src/agent/run.py \
  --input findings/snyk-export.json \
  --output reports/
```

The tool accepts findings from any scanner that outputs:

```json
{
  "cve_id": "CVE-2025-29927",
  "package": "next@14.1.0",
  "language": "javascript",
  "cwe": "CWE-22",
  "file": "src/middleware.ts",
  "line": 47
}
```

**312 findings queued. Processing begins.**

---

## Stage 2: Exploit Validation Agent

This is the core of the system. For each finding, five specialized sub-agents run in sequence.

### The Five Sub-Agents

```
Finding → Advisory Agent → Source-to-Sink Analyzer → Payload Generator
                                                              ↓
                                              Sandbox Executor (Docker)
                                                              ↓
                                              Result Interpreter → Report
```

**Total runtime per finding: ~193 seconds.**

---

### Live Example: CVE-2025-29927 in Next.js 14.1.0

#### Sub-Agent 1: Advisory Agent (Gemini Flash)

Loads the CVE advisory, vendor bulletin, and all referenced content.

```
Loading CVE-2025-29927...
→ NVD entry: Middleware bypass via path traversal in Next.js
→ GitHub advisory: GHSA-xxxx-xxxx loaded
→ Vendor patch diff: middleware.ts normalization fix identified
→ Affected versions confirmed: 14.1.0, 14.2.0-14.2.24, 15.x before 15.2.3
```

#### Sub-Agent 2: Source-to-Sink Analyzer (Gemini Pro)

Traces untrusted input through Jordan's actual codebase to find where it reaches the vulnerable sink.

```
Analyzing src/middleware.ts:47...
→ Entry point: req.headers['x-middleware-subrequest'] (untrusted)
→ Flow: headers → path normalization → middleware routing
→ Sink: _next/image route bypass — skips authentication check
→ Gap: path normalization missing before auth middleware evaluation
→ Confidence: HIGH
```

#### Sub-Agent 3: Payload Generator (Gemini Pro)

Generates 10–30 library-aware payloads targeting the specific version's internals.

```
Generating payloads for next@14.1.0...
→ Understanding middleware routing architecture...
→ Payload 1: x-middleware-subrequest: pages/_middleware
→ Payload 2: x-middleware-subrequest: middleware
→ Payload 3: x-middleware-subrequest: src/middleware
... (18 total payloads)
```

#### Sub-Agent 4: Sandbox Executor (Docker)

Runs each payload in an isolated, ephemeral container. Zero network access. Resource-capped. Full audit trail.

```
Spinning up sandbox: next@14.1.0 (Docker)...
→ Container: sandbox-exec-2026-04-07-a3f91b (ephemeral)
→ Network: isolated (no outbound)
→ Testing payload 1... ✗
→ Testing payload 7... ✓ BYPASS CONFIRMED
→ Authenticated route /api/admin returned 200 without credentials
→ Container destroyed
```

#### Sub-Agent 5: Result Interpreter (Gemini Flash)

Parses execution output and produces the structured exploit report.

---

### The Exploit Report

```json
{
  "cve_id": "CVE-2025-29927",
  "package": "next@14.1.0",
  "status": "CONFIRMED",
  "validation_date": "2026-04-07T09:14:52Z",
  "execution_id": "exec-2026-04-07-a3f91b",

  "summary": "Next.js middleware can be bypassed by setting the x-middleware-subrequest header, allowing unauthenticated access to routes protected by middleware authentication checks.",

  "impact": "An unauthenticated attacker can access any route protected by Next.js middleware, including admin panels, API endpoints, and authenticated user data. Authentication is entirely bypassed.",

  "reproduction_steps": [
    "Install next@14.1.0",
    "Create a middleware.ts that gates /api/admin behind authentication",
    "Send: GET /api/admin HTTP/1.1 with header x-middleware-subrequest: pages/_middleware",
    "Response: 200 OK — admin route returned without authentication"
  ],

  "ttp": {
    "technique_id": "T1190",
    "technique_name": "Exploit Public-Facing Application",
    "tactic": "Initial Access",
    "real_world_precedent": "MOVEit (2023) — same technique, T1190 + T1005, 2,000+ organizations compromised"
  }
}
```

---

### False Positive — Equally Valuable

Not every finding is real. For 71 of Jordan's 312 findings, the agent returns:

```json
{
  "cve_id": "CVE-2024-56201",
  "package": "jinja2@3.1.4",
  "status": "FALSE_POSITIVE",
  "confidence": 0.97,
  "reason": "The SSTI vulnerability requires use of jinja2.Template() with unsanitized user input. Static analysis of the codebase shows all template rendering uses environment.get_template() with pre-approved template names from a whitelist. The vulnerable code path is unreachable.",
  "developer_guidance": "This finding can be closed. Your template rendering pattern is safe. No action required."
}
```

**71 false positives eliminated = 71 engineer-hours saved this sprint.**

---

## Stage 3: TTP Mapping and Breach Correlation

For every confirmed finding, the system maps to MITRE ATT&CK and correlates with real breach incidents.

### CVE-2025-29927 Mapped

| Dimension | Value |
|-----------|-------|
| **ATT&CK Technique** | T1190 — Exploit Public-Facing Application |
| **Tactic** | Initial Access |
| **Secondary technique** | T1078 — Valid Accounts (bypassed) |

### Breach Correlation

The system scores real incidents by technique overlap, recency, and industry match:

| Breach | Year | Technique match | Industry | Relevance |
|--------|------|-----------------|----------|-----------|
| MOVEit Transfer | 2023 | T1190, T1005 | SaaS/Enterprise | **0.94** |
| Exchange ProxyShell | 2021 | T1190, T1059 | Enterprise | 0.81 |
| Kaseya VSA | 2021 | T1190, T1199 | SaaS | 0.76 |

**What Jordan tells her CISO:**
> "CVE-2025-29927 in Next.js enables the same initial access technique used in the MOVEit breach, which compromised 2,000 organizations and exposed 60 million records. We confirmed it works in our stack. Fix is ready."

That is a different conversation than "we have a Critical CVSS 9.8."

---

## Stage 4: Remediation Operations

### Security Tasks (not CVE lists)

The system groups related findings into developer-ready Security Tasks — consolidating CVEs that share the same fix, package, and compliance requirement.

Jordan's 312 findings collapse into **47 Security Tasks**.

**Example Task — groups 4 related CVEs:**

```json
{
  "task_id": "task-next-middleware-bypass",
  "title": "Upgrade Next.js to eliminate middleware authentication bypass",

  "security_outcome": "Eliminates authentication bypass enabling T1190 initial access. Same technique as MOVEit 2023.",

  "findings": [
    { "cve_id": "CVE-2025-29927", "status": "CONFIRMED" },
    { "cve_id": "CVE-2024-46982", "status": "CONFIRMED" },
    { "cve_id": "CVE-2024-34351", "status": "CONFIRMED" },
    { "cve_id": "CVE-2024-56332", "status": "CONFIRMED" }
  ],

  "fix": "Upgrade next from 14.1.0 to 15.2.3",
  "estimated_effort_hours": 2.0,
  "priority": 1,

  "compliance": [
    "NIST-SSDF-RV.1.1 — Verify remediation of identified vulnerabilities",
    "SLSA-L2 — Build integrity requirements",
    "OpenSSF-Scorecard — Dependency pinning"
  ],

  "business_justification": "Authentication bypass on your customer-facing API routes. MOVEit used identical technique. 2 hours to fix. Fix this sprint.",

  "pr_url": "https://github.com/org/saas-app/pull/2847"
}
```

### Auto-Generated Pull Request

The system prepares the PR. The engineer reviews and merges.

```
PR #2847: security: upgrade next to 15.2.3 (CVE-2025-29927 + 3 related)

Changes:
  package.json: "next": "14.1.0" → "15.2.3"
  package-lock.json: updated

Security context:
  Eliminates middleware authentication bypass (T1190)
  Confirmed exploitable in sandbox — exec-2026-04-07-a3f91b
  Real-world precedent: MOVEit 2023

Compliance evidence attached: NIST SSDF RV.1.1, SLSA L2
```

---

## Stage 5: Compliance Automation

Every validation run produces timestamped audit evidence as a byproduct — not generated in bulk at audit time.

```json
{
  "evidence_id": "ev-2026-04-07-next-upgrade",
  "timestamp": "2026-04-07T11:42:18Z",
  "finding": "CVE-2025-29927",
  "action": "REMEDIATED",
  "remediation": "next upgraded from 14.1.0 to 15.2.3 via PR #2847",
  "validation": "Post-fix sandbox execution confirmed bypass no longer possible",
  "frameworks": {
    "NIST_SSDF": "RV.1.1 — vulnerability identified, exploitability verified, remediation applied and validated",
    "SLSA": "L2 — provenance recorded, build integrity maintained",
    "OpenSSF": "Scorecard dependency requirement satisfied"
  }
}
```

Jordan's auditor gets a continuous evidence trail — not a spreadsheet assembled two weeks before the audit.

---

## The Full Journey

```
Day 1: Intake
  312 findings from Snyk
  → 193 seconds per finding × parallel processing
  → 241 confirmed exploitable
  → 71 false positives eliminated

Day 2: Triage
  241 confirmed findings → 47 Security Tasks
  → Ranked by ATT&CK technique + breach correlation
  → Business justification written for each task
  → PRs prepared and ready for review

Day 3: Sprint Planning
  Engineers pull Security Tasks instead of CVE lists
  → Each task has: confirmed exploit, real breach context,
    effort estimate, fix ready, compliance evidence attached
  → 68% remediation engagement (vs. 32% on CVE lists)

Board Meeting: CISO Slides
  "We confirmed 241 exploitable vulnerabilities,
   prioritized by breach technique match.
   Top 12 tasks this sprint address the highest-risk exposure.
   All remediation evidence is audit-ready."
```

---

## What This Is Not

| Not this | Because |
|----------|---------|
| A vulnerability scanner | It does not find vulnerabilities — it validates and contextualizes them |
| A penetration test | It validates specific CVEs in specific packages, not full attack simulation |
| A CVSS replacement | It operates after CVSS — it proves which CVSS scores are real in your context |
| A compliance checkbox | Evidence is a byproduct of actual remediation, not generated for appearances |

!!! warning "Responsible use"
    All sandbox execution runs in isolated Docker containers with zero network access and strict resource limits. Exploit validation is performed only against your own codebase and declared dependencies. Never run against systems you do not own or have explicit authorization to test.
