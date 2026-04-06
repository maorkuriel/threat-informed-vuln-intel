# Running the Pipeline

## Input Format

TIVI accepts a standard JSON findings file:

```json
[
  {
    "finding_id": "unique-id",
    "source": "sast",
    "cve_id": "CVE-2025-XXXX",
    "cwe_id": "CWE-22",
    "package": "example-package@2.1.0",
    "language": "python",
    "repository": "https://github.com/org/repo",
    "affected_file": "src/handlers/files.py",
    "affected_line": 47
  }
]
```

## Running Modes

```bash
# Single finding — full validation
python src/agent/run.py --cve CVE-2025-XXXX --package "pkg@1.0.0"

# Batch — all findings from file
python src/pipeline/run_pipeline.py --findings findings.json

# Batch with PR automation
python src/pipeline/run_pipeline.py --findings findings.json --create-prs

# Compliance report only (skip validation, use existing results)
python src/reporting/compliance_report.py \
  --results reports/ \
  --framework nist-ssdf \
  --output audit_evidence.pdf

# Daily update (for CI/CD integration)
bash scripts/daily_pipeline.sh
```

## CI/CD Integration

```yaml
# .github/workflows/tivi-validation.yml
name: TIVI Vulnerability Validation

on:
  schedule:
    - cron: '0 6 * * *'    # Daily at 6am
  workflow_dispatch:

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate SBOM
        run: python src/ingestion/sbom_extractor.py --output findings.json
      - name: Run TIVI validation
        env:
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
        run: |
          python src/pipeline/run_pipeline.py \
            --findings findings.json \
            --output-dir reports/ \
            --fail-on-confirmed-priority 0
      - name: Upload report
        uses: actions/upload-artifact@v4
        with:
          name: tivi-report
          path: reports/
```
