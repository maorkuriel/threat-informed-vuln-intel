# Prerequisites

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| Python | 3.11+ | 3.12+ |
| Docker | 24.0+ | Latest |
| RAM | 8 GB | 16 GB |
| Storage | 20 GB | 50 GB |
| OS | Linux / macOS / WSL2 | Ubuntu 22.04 LTS |

## Required API Keys

### Google Gemini API
- Sign up at [Google AI Studio](https://aistudio.google.com/)
- Create API key — used for all AI agents (Advisory, Source-to-Sink, Payload, Result)
- Models used: `gemini-2.0-pro` (reasoning), `gemini-2.0-flash` (fast tasks)

### GitHub Token (for PR automation)
- Create a fine-grained personal access token with: `contents:write`, `pull_requests:write`
- Scoped to the repositories you want TIVI to submit fixes to

## Installation

```bash
git clone https://github.com/maorkuriel/threat-informed-vuln-intel.git
cd threat-informed-vuln-intel
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# Edit .env with your API keys
```

## Environment Variables

```bash
# .env
GEMINI_API_KEY=your-gemini-api-key
GITHUB_TOKEN=your-github-token        # for PR automation
NVD_API_KEY=your-nvd-key             # optional, increases rate limits

# Optional: notification integrations
SLACK_WEBHOOK_URL=https://hooks.slack.com/...
```

!!! warning
    Never commit `.env`. It is included in `.gitignore` by default.
