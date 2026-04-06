# Research Foundation

!!! abstract "Overview"
    TIVI is grounded in peer-reviewed academic research and validated attack-technique-to-task mappings produced through independent, multi-method analysis.

## Primary Research

!!! quote "Citation"
    Akhoundali, J., Hamidi, H., Rietveld, K., & Gadyatskaya, O. (2025).
    **"Eradicating the Unseen: Detecting, Exploiting, and Remediating a Path Traversal Vulnerability across GitHub."**
    *Proceedings of the 20th ACM Asia Conference on Computer and Communications Security (AsiaCCS 2025).*
    [https://dl.acm.org/doi/10.1145/3708821.3736220](https://dl.acm.org/doi/10.1145/3708821.3736220)

This research demonstrated the feasibility of automated, large-scale vulnerability validation — confirming exploitation across **1,756 real-world repositories** using a pipeline combining static analysis, dynamic sandbox execution, and LLM-assisted remediation. TIVI generalizes and extends this methodology beyond a single vulnerability class.

## TTP-to-Task Mapping Research

The validated attack-technique-to-task mappings in TIVI are derived from research analyzing **106 documented breach reports**, producing **251 high-confidence mappings** verified through four independent methods:

| Method | Description |
|--------|-------------|
| **Transitive Mapping** | Traces relationships between ATT&CK techniques, CWEs, and remediation tasks through published vulnerability databases |
| **LLM Analysis** | Uses large language models to synthesize technique-task relationships from security literature |
| **Framework Alignment** | Cross-references mappings against NIST SSDF, SLSA, and OpenSSF task taxonomies |
| **Breach Intelligence** | Validates mappings against attacker behavior documented in incident reports |

A mapping is classified as **high-confidence** only when at least 3 of 4 methods agree. This ensures that TIVI's prioritization recommendations are based on evidence rather than speculation.

## Why Academic Validation Matters

Commercial security tools produce mappings based on proprietary analysis that cannot be independently verified. TIVI's foundational mappings are:

- **Peer-reviewed** — evaluated by the academic security community
- **Reproducible** — the methodology is published and can be replicated
- **Independent** — four distinct methods reduce the risk of systematic bias
- **Auditable** — each mapping can be traced to its source evidence

This creates a level of trustworthiness that vendor-proprietary analysis cannot match — which is why enterprise security leaders and compliance teams can use TIVI's output to defend decisions to auditors and boards.

## LLM Contamination Research

The primary research also documented a significant finding about AI-generated code quality:

!!! warning "AI Code Generation Vulnerability"
    In testing, **95% of LLM-generated code samples** for common security-sensitive patterns contained the same class of vulnerability being studied. Even when models were explicitly asked to generate **secure** implementations, **70% still produced vulnerable code**.

This finding motivates TIVI's validation-first approach: given that AI-generated code cannot be assumed secure, automated exploit validation becomes essential infrastructure for any development workflow that incorporates LLM assistance.

## Continuing Research

TIVI's mapping dataset is updated to align with:

- MITRE ATT&CK bi-annual framework updates
- Emerging breach intelligence from public incident reports
- New CWE entries and taxonomy updates
- Evolving compliance framework requirements (NIST SSDF, SLSA, OpenSSF)
