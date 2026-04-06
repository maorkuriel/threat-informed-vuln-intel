# TTP Mapping — Overview

!!! abstract "Overview"
    TIVI maps every confirmed vulnerability to MITRE ATT&CK Tactics, Techniques, and Procedures (TTPs) — connecting application security findings to the adversary behaviors documented in real-world breaches.

## Why TTP Mapping Changes Everything

Without TTP mapping, a security finding is a CVE number and a severity score. With it:

| Without TTP Mapping | With TTP Mapping |
|--------------------|-----------------|
| "CVE-2025-XXXX, CVSS 9.1" | "This enables T1195 — Supply Chain Compromise, the same technique used in SolarWinds" |
| Security team prioritizes by CVSS | Security team prioritizes by breach relevance |
| Board asked: "Are we safe from SolarWinds-style attacks?" Answer: "Unknown" | Board asked the same question. Answer: "Yes/No — here's the evidence" |

## The 251 Validated Mappings

TIVI's TTP-to-task mappings were produced through the four-method validation process described in the [Research Foundation](../introduction/research_foundation.md):

- **251 high-confidence mappings** — agreed upon by at least 3 of 4 methods
- **106 breach reports** analyzed as source material
- **Bi-annual updates** aligned with MITRE ATT&CK release cycle

## Mapping Sections

| Section | Description |
|---------|-------------|
| [CVE to ATT&CK](cve_to_attack.md) | How vulnerability classes map to ATT&CK techniques |
| [Validated Mappings](validated_mappings.md) | The full mapping table with confidence scores |
| [Breach Correlation](breach_correlation.md) | Which real-world incidents used each technique |
