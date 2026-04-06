# SLSA & OpenSSF

!!! abstract "Overview"
    TIVI maps vulnerability remediation tasks to SLSA (Supply-chain Levels for Software Artifacts) and OpenSSF Scorecard checks — the two most widely adopted supply chain security frameworks.

## SLSA Coverage

SLSA defines four levels of supply chain security maturity:

| SLSA Level | Key Requirements | TIVI Tasks That Address It |
|-----------|-----------------|--------------------------|
| Level 1 | Build process documented, provenance generated | Build pipeline configuration audit |
| Level 2 | Hosted build platform, signed provenance | CI/CD integrity controls |
| Level 3 | Hardened build platform, two-party review | Build environment isolation |
| Level 4 | Hermetic builds, reproducible | Hermetic build verification |

### TIVI → SLSA Mapping

| ATT&CK Technique | TIVI Task | SLSA Requirement |
|-----------------|-----------|-----------------|
| T1195.001 Supply Chain Compromise | Implement artifact signing | SLSA L2 — Signed provenance |
| T1554 Compromise Software Binary | Build integrity verification | SLSA L3 — Hardened platform |
| T1199 Trusted Relationship | Dependency integrity checks | SLSA L2 — Authenticated provenance |
| T1552.001 Credentials in Files | Secrets management | SLSA L2 — Secure build |

## OpenSSF Scorecard Coverage

OpenSSF Scorecard runs 18 automated checks against a repository. TIVI's remediation tasks directly address 12 of them:

| Scorecard Check | Score Improvement | TIVI Task |
|----------------|------------------|-----------|
| `Vulnerabilities` | Critical | Exploit validation + fix |
| `Code-Review` | High | PR-based remediation workflow |
| `Branch-Protection` | Medium | Branch policy enforcement |
| `Signed-Releases` | High | Artifact signing implementation |
| `Dependency-Update-Tool` | Medium | Automated dependency tracking |
| `Pinned-Dependencies` | High | Dependency pinning |
| `SAST` | Medium | Static analysis integration |
| `Token-Permissions` | Medium | Least-privilege CI tokens |
| `Security-Policy` | Low | Security.txt / SECURITY.md |
| `License` | Low | License verification |
| `CI-Tests` | Medium | Test suite completeness |
| `Dangerous-Workflow` | High | CI workflow hardening |
