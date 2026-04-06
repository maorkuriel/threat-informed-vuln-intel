# Remediation Operations — Overview

!!! abstract "Overview"
    RemOps is the practice of treating vulnerability remediation as a scalable workflow discipline — with automated intake, prioritization, task assignment, and evidence generation.
    TIVI is the engine that powers this workflow.

## The Shift: Vulnerability Lists → Security Tasks

Traditional vulnerability management gives developers a ticket for every CVE. TIVI groups related findings into **Security Tasks** — developer-facing work items with:

- A clear, meaningful title (not a CVE number)
- Concrete security outcome
- All related findings bundled
- Specific code fix included
- Compliance requirements pre-attached
- Estimated developer effort

### Example

**Before (traditional approach):**
```
TICKET: Fix CVE-2025-1234 in lodash@4.17.20 (CVSS 9.8)
TICKET: Fix CVE-2025-5678 in lodash@4.17.20 (CVSS 7.5)
TICKET: Fix CVE-2025-9012 in lodash@4.17.20 (CVSS 8.1)
TICKET: Fix CVE-2025-3456 in underscore@1.12.0 (CVSS 6.5)
```

**After (TIVI Security Task):**
```
TASK: Upgrade utility library dependencies to eliminate prototype pollution risk
OUTCOME: Eliminates 4 related CVEs enabling T1190, satisfies NIST SSDF RV.1.1
EFFORT: ~30 minutes
DETAILS: Upgrade lodash to 4.17.21+, underscore to 1.13.0+
         [Link to automated PR with fix already prepared]
```

## RemOps Modules

| Module | Page | What It Does |
|--------|------|-------------|
| Security Tasks | [Security Tasks](security_tasks.md) | Groups findings into developer-ready tasks |
| AI Fix Generation | [Fix Generation](fix_generation.md) | Gemini-powered code fix generation |
| PR Automation | [PR Automation](pr_automation.md) | Auto-creates PRs with fixes and context |
