# Breach Correlation

!!! abstract "Overview"
    TIVI correlates every confirmed vulnerability with real-world breach incidents that used the same ATT&CK technique — transforming an abstract CVE number into a concrete threat narrative that security leaders can present to boards.

## Why This Matters

When a board asks "could we be hit by a SolarWinds-style attack?", current security tools cannot answer. TIVI can — because it knows which vulnerabilities in your environment enable T1195 (Supply Chain Compromise), the technique used in that breach.

## Breach Intelligence Database

TIVI maintains a curated database of documented breaches, indexed by ATT&CK technique:

| Breach | Year | Techniques Used | Impact |
|--------|------|----------------|--------|
| SolarWinds | 2020 | T1195.001 | 18,000 organizations compromised |
| Log4Shell | 2021 | T1190, T1059 | Millions of servers exposed |
| XZ Utils | 2024 | T1195.001, T1554 | Embedded backdoor in widely-used library |
| MOVEit | 2023 | T1190, T1005 | 2,000+ organizations, 60M records stolen |
| Kaseya VSA | 2021 | T1199, T1486 | 1,500 businesses ransomed via MSP |
| Exchange ProxyShell | 2021 | T1190, T1059 | Mass exploitation of email servers |

## Breach Relevance Scoring

Not all breach correlations are equally relevant to a given organization. TIVI scores breach relevance based on:

```python
def score_breach_relevance(breach: Breach, context: AssetContext) -> float:
    score = 0.0
    
    # Recency: more recent breaches weighted higher
    years_ago = (today - breach.date).days / 365
    score += max(0, 1.0 - (years_ago * 0.15))
    
    # Industry match: breach targeting same sector weighted higher
    if breach.target_sector == context.organization_sector:
        score += 0.4
    
    # Technique match: how many matching techniques
    technique_overlap = len(breach.techniques & asset_ttp_set) / len(breach.techniques)
    score += technique_overlap * 0.4
    
    # Severity: breach impact magnitude
    score += breach.severity_weight * 0.2
    
    return min(score, 1.0)
```

## Board-Level Reporting

TIVI generates natural-language breach exposure reports for security leadership:

```
BREACH EXPOSURE REPORT — [Organization] — [Date]

Your environment contains 3 vulnerabilities that enable techniques
used in the SolarWinds supply chain attack (T1195.001):

  HIGH PRIORITY:
  - CVE-2025-XXXX in build-tool@1.2.3 — Confirmed exploitable
    Impact: Allows injection of malicious code into build artifacts
    Same technique: T1195.001 used in SolarWinds (2020)

  MEDIUM PRIORITY:
  - CVE-2025-YYYY in ci-plugin@4.0.1 — Confirmed exploitable
    Impact: Allows build pipeline manipulation

RECOMMENDED ACTION:
  Remediation task "Implement build artifact signing" (NIST SSDF PO.5.2)
  addresses all 3 findings and satisfies 2 compliance requirements.
  Estimated developer effort: 4 hours.
```
