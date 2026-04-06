# Quick Start

!!! abstract "Overview"
    Validate your first vulnerability in under 10 minutes.

## Step 1: Install

```bash
git clone https://github.com/maorkuriel/threat-informed-vuln-intel.git
cd threat-informed-vuln-intel
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# Add GEMINI_API_KEY to .env
```

## Step 2: Validate a Single Vulnerability

```bash
python src/agent/run.py \
  --cve CVE-2025-EXAMPLE \
  --package "example-package@2.1.0" \
  --language python

# Output (after ~193 seconds):
# ✓ Advisory loaded
# ✓ Source-to-sink traced: routes/handler.py:47
# ✓ 18 payloads generated
# ✓ Sandbox executed: exec-2026-04-06-a3f91b
# ✓ CONFIRMED EXPLOITABLE
#
# Exploit Report: reports/CVE-2025-EXAMPLE-exploit-report.json
# TTP Mapping: T1190 (Exploit Public-Facing Application)
# Compliance: NIST SSDF RV.1.3, RV.2.1
# Fix ready: patches/CVE-2025-EXAMPLE.diff
```

## Step 3: Run on a Full Finding List

```bash
# Export findings from your SAST/SCA tool as JSON
# (see docs/setup/running.md for format)

python src/pipeline/run_pipeline.py \
  --findings findings.json \
  --output-dir reports/ \
  --create-prs          # auto-create GitHub PRs for confirmed findings
```

## Step 4: View Results

```bash
# Summary table in terminal
python src/reporting/view_results.py --input reports/

# HTML report
python src/reporting/html_report.py \
  --input reports/ \
  --output reports/tivi_report.html
# Open in browser
```

!!! success "What You'll Have After Quick Start"
    - Confirmed vs. false positive determination for each finding
    - Working PoC for every confirmed vulnerability
    - TTP mapping to MITRE ATT&CK
    - Draft PRs with validated fixes (if `--create-prs` used)
    - Compliance evidence records
