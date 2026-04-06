# CVE to ATT&CK Mapping

!!! abstract "Overview"
    This page explains how TIVI maps vulnerability classes (CWEs) to MITRE ATT&CK techniques, enabling security teams to answer "which attack techniques does our vulnerability backlog enable?"

## Mapping Architecture

The mapping operates in two layers:

```
CVE → CWE (vulnerability class) → ATT&CK Technique(s)
```

Layer 1 (CVE → CWE) uses TIVI's AI-powered enrichment to classify each vulnerability.
Layer 2 (CWE → ATT&CK) uses the validated mapping table derived from breach research.

## Core Mapping Table

| CWE | Vulnerability Class | Primary ATT&CK Techniques | Tactic |
|-----|--------------------|--------------------------|----|
| CWE-22 | Path Traversal | T1083, T1005 | Discovery, Collection |
| CWE-78 | Command Injection | T1059, T1190 | Execution, Initial Access |
| CWE-89 | SQL Injection | T1190, T1005, T1552 | Initial Access, Collection, Credential Access |
| CWE-79 | Cross-Site Scripting | T1185, T1539 | Collection |
| CWE-502 | Unsafe Deserialization | T1059, T1190 | Execution, Initial Access |
| CWE-918 | SSRF | T1090, T1046 | Command & Control, Discovery |
| CWE-611 | XXE | T1083, T1005 | Discovery, Collection |
| CWE-287 | Authentication Bypass | T1078, T1550 | Defense Evasion, Lateral Movement |
| CWE-798 | Hardcoded Credentials | T1552.001 | Credential Access |
| CWE-94 | Code Injection | T1059, T1190 | Execution, Initial Access |
| CWE-434 | Unrestricted Upload | T1190, T1059.007 | Initial Access, Execution |
| CWE-295 | Certificate Validation | T1557, T1040 | Collection |

## Supply Chain Techniques

Supply chain vulnerabilities map to techniques that have driven the highest-profile breaches:

| ATT&CK Technique | Description | Notable Breaches |
|-----------------|-------------|-----------------|
| T1195.001 | Compromise Software Supply Chain | SolarWinds (2020), XZ Utils (2024) |
| T1195.002 | Compromise Software Dependencies | Event-stream (2018), ua-parser-js (2021) |
| T1554 | Compromise Client Software Binary | CCleaner (2017) |
| T1199 | Trusted Relationship | Kaseya (2021) |

## How TIVI Uses These Mappings

```python
def enrich_with_ttp(finding: Finding) -> EnrichedFinding:
    # 1. Get CWE from finding (or assign via AI enrichment)
    cwe = finding.cwe_id or assign_cwe(finding)
    
    # 2. Look up ATT&CK techniques from validated mapping table
    techniques = ttp_mapping_table.lookup(cwe)
    
    # 3. Correlate with real-world breach data
    breaches = breach_database.find_by_techniques(techniques)
    
    # 4. Score breach relevance (recency + severity + industry)
    breach_relevance = score_breach_relevance(breaches, finding.asset_context)
    
    return EnrichedFinding(
        finding=finding,
        ttp_techniques=techniques,
        breach_correlations=breaches,
        breach_relevance_score=breach_relevance
    )
```

!!! tip
    The breach relevance score allows TIVI to answer sector-specific questions: "Which of our vulnerabilities enable techniques used in attacks against financial institutions in the last 12 months?"
