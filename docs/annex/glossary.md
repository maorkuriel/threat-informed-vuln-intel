# Glossary

| Term | Definition |
|------|-----------|
| **TTP** | Tactics, Techniques, and Procedures — the behavioral patterns of threat actors, as documented in MITRE ATT&CK |
| **MITRE ATT&CK** | A globally accessible knowledge base of adversary tactics and techniques based on real-world observations |
| **PoC** | Proof of Concept — a working exploit demonstrating a vulnerability is real and exploitable in a specific context |
| **Source-to-Sink** | Data flow analysis: tracing untrusted attacker-controlled input (source) through code to a dangerous operation (sink) |
| **Library-Aware** | Exploit generation that analyzes the specific package source at the affected version, rather than using generic payload templates |
| **RemOps** | Remediation Operations — treating vulnerability remediation as a scalable workflow discipline with automated intake, prioritization, and evidence generation |
| **Security Task** | TIVI's developer-facing work unit — a group of related vulnerabilities consolidated into one actionable item with fix, context, and compliance requirements |
| **SSDF** | Secure Software Development Framework (NIST SP 800-218) — a framework of practices for secure software development |
| **SLSA** | Supply-chain Levels for Software Artifacts — a security framework for software supply chain integrity, published by Google and OpenSSF |
| **OpenSSF Scorecard** | An automated tool by the Open Source Security Foundation that assesses open source projects against security best practices |
| **P-SSCRM** | Product-Security Software Supply Chain Risk Management — a task framework mapping ATT&CK techniques to specific security practices |
| **High-Confidence Mapping** | A TTP-to-task mapping verified by at least 3 of 4 independent validation methods |
| **Exploit Status** | TIVI's determination for each finding: CONFIRMED, FALSE_POSITIVE, PARTIAL, or UNCONFIRMED |
| **Sandbox** | An isolated Docker container used for safe exploit execution — no production systems touched |
| **Advisory Agent** | TIVI sub-agent responsible for loading CVE advisories and reference content |
| **Audit Evidence** | Timestamped, structured records generated automatically when findings are validated and remediated |
| **MTTR** | Mean Time to Remediate — the average elapsed time between a vulnerability being identified and resolved |
| **CWE** | Common Weakness Enumeration — a taxonomy of software and hardware weakness types |
| **CVE** | Common Vulnerabilities and Exposures — a standardized identifier for publicly known vulnerabilities |
| **CVSS** | Common Vulnerability Scoring System — a framework for rating vulnerability severity (not exploitability) |
