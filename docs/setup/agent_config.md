# Agent Configuration

```yaml
# config/agent_config.yaml

agent:
  gemini:
    advisory_model: gemini-2.0-flash-001
    analysis_model: gemini-2.0-pro-001      # Deep reasoning for source-to-sink
    generation_model: gemini-2.0-pro-001    # Payload and fix generation
    interpretation_model: gemini-2.0-flash-001
    temperature:
      advisory: 0.1
      analysis: 0.2
      generation: 0.4    # Slightly higher for creative payload variants
      interpretation: 0.1
    max_retries: 3

sandbox:
  docker:
    memory_limit: "512m"
    cpu_limit: "0.5"
    timeout_seconds: 300
    network_mode: "bridge"    # Isolated — no external network
    auto_cleanup: true

pipeline:
  max_concurrent_validations: 5
  validation_timeout_seconds: 600
  payloads_per_finding: 20
  confidence_threshold: 0.85   # Minimum confidence to report CONFIRMED

disclosure:
  auto_create_prs: false        # Set true to enable PR automation
  pr_target_branch: "main"
  require_human_review: true    # Require approval before PR submission
```
