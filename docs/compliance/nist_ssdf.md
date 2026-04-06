# NIST SSDF Mapping

!!! abstract "Overview"
    Every Security Task in TIVI maps to specific NIST SP 800-218 (Secure Software Development Framework) requirements — enabling organizations to demonstrate continuous SSDF compliance as a natural byproduct of remediation.

## SSDF Structure

NIST SSDF organizes secure development practices into 4 groups:

| Group | ID | Description |
|-------|----|-------------|
| Prepare the Organization | PO | Ensure people, processes, and technology are prepared |
| Protect the Software | PS | Protect all components of software from tampering |
| Produce Well-Secured Software | PW | Apply security practices during development and testing |
| Respond to Vulnerabilities | RV | Identify residual vulnerabilities and respond appropriately |

## Key TIVI Mappings

### Respond to Vulnerabilities (RV) — Most Directly Addressed

| SSDF Requirement | Description | TIVI Capability |
|-----------------|-------------|----------------|
| RV.1.1 | Gather and analyze information about potential vulnerabilities | CVE enrichment + exploit validation |
| RV.1.2 | Review and analyze vulnerability advisories | Advisory Agent automated loading |
| RV.1.3 | Gather evidence to confirm vulnerability | Sandbox execution → PoC |
| RV.2.1 | Analyze the vulnerability to understand its causes | Source-to-Sink analysis |
| RV.2.2 | Develop and implement fixes | AI fix generation + PR automation |
| RV.3.1 | Test fixes to verify they work | Sandbox regression testing |
| RV.3.2 | Monitor known vulnerabilities | Continuous CVE tracking |

### Produce Well-Secured Software (PW) — Addressed via Task Mapping

| SSDF Requirement | Description | P-SSCRM Task |
|-----------------|-------------|-------------|
| PW.4.1 | Protect code from unauthorized access | Code signing (T1195) |
| PW.4.4 | Verify integrity of third-party software | Dependency verification (T1195) |
| PW.7.2 | Conduct security testing | Automated exploit validation |
| PW.8.2 | Establish and follow remediation processes | RemOps workflow |

## SSDF Evidence Generation

When a developer merges a TIVI-generated fix, TIVI automatically creates SSDF evidence:

```json
{
  "framework": "NIST SP 800-218",
  "requirements_satisfied": ["RV.1.3", "RV.2.1", "RV.2.2", "RV.3.1"],
  "finding_id": "finding-uuid",
  "cve_id": "CVE-2025-XXXX",
  "validation_evidence": {
    "exploit_confirmed": true,
    "execution_id": "exec-uuid",
    "timestamp": "2026-04-06T10:23:11Z"
  },
  "remediation_evidence": {
    "fix_type": "code_change",
    "pr_url": "https://github.com/.../pull/42",
    "merged_at": "2026-04-06T14:55:00Z",
    "regression_test_passed": true
  }
}
```
