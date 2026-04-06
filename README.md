# Threat-Informed Vulnerability Intelligence (TIVI)

> *"In 2024, the world wanted to see their risks. In 2026, the world wants to prove and fix them."*

**TIVI** is an AI-powered platform that transforms vulnerability findings into exploitation evidence — generating working proof-of-concept exploits in isolated sandboxes, mapping results to MITRE ATT&CK techniques, and producing compliance-ready remediation tasks.

---

## The Core Value

| Old Model | TIVI |
|-----------|------|
| Thousands of CVE tickets | Grouped Security Tasks with clear outcomes |
| CVSS score determines priority | Exploitation evidence determines priority |
| Developer gets a CVE number | Developer gets a proof, a fix, and compliance context |
| Manual triage: 4–6 hours per finding | Automated validation: ~193 seconds |
| 68% of findings ignored | Evidence-backed findings acted on |

---

## Full Documentation

**[https://maorkuriel.github.io/threat-informed-vuln-intel](https://maorkuriel.github.io/threat-informed-vuln-intel)**

---

## Architecture

```
Vulnerability Finding
       │
       ▼
Exploit Validation Agent  ←── Multi-Agent (Gemini Pro + Flash)
  ├── Advisory Loader
  ├── Source-to-Sink Analyzer
  ├── Payload Generator (Library-Aware)
  ├── Sandbox Executor (Docker, isolated)
  └── Result Interpreter
       │
       ▼
Threat Intelligence Layer
  ├── TTP Mapper → MITRE ATT&CK
  ├── Breach Correlator
  └── Risk Scorer
       │
       ▼
Remediation Operations (RemOps)
  ├── Security Task Generator (P-SSCRM)
  ├── AI Fix Generator
  ├── Compliance Mapper (NIST SSDF · SLSA · OpenSSF)
  ├── PR Automation
  └── Audit Evidence Generator
```

---

## Quick Start

```bash
git clone https://github.com/maorkuriel/threat-informed-vuln-intel.git
cd threat-informed-vuln-intel
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # add GEMINI_API_KEY

# Validate a single vulnerability
python src/agent/run.py --cve CVE-2025-XXXX --package "pkg@affected-version"

# Batch validate from a findings file
python src/pipeline/run_pipeline.py --findings findings.json --output-dir reports/
```

---

## Research Foundation

TIVI is grounded in peer-reviewed research:

> Akhoundali et al., *"Eradicating the Unseen: Detecting, Exploiting, and Remediating a Path Traversal Vulnerability across GitHub"*, AsiaCCS 2025.
> [https://dl.acm.org/doi/10.1145/3708821.3736220](https://dl.acm.org/doi/10.1145/3708821.3736220)

The validated TTP-to-task mappings are derived from analysis of **106 breach reports** using four independent validation methods, producing **251 high-confidence mappings**.

---

## License

MIT — see [LICENSE](LICENSE)
