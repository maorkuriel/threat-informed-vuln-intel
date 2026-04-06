# AI-Driven Fix Generation

!!! abstract "Overview"
    TIVI uses Google Gemini to generate targeted code fixes for confirmed vulnerabilities. Unlike generic "upgrade to latest" guidance, TIVI's fixes are context-aware — understanding the specific code pattern, library API, and application architecture.

## Fix Generation Principles

### Minimal Change

Fixes modify only what is necessary to eliminate the vulnerability. They do not:
- Refactor surrounding code
- Change variable names
- Add unrelated improvements
- Modify behavior outside the vulnerable code path

### Correct, Not Just Safe

A fix that breaks functionality is worse than no fix — it guarantees the PR gets rejected, and security debt accumulates. TIVI validates generated fixes by:

1. Running the application's existing tests in the sandbox
2. Verifying the fix eliminates the vulnerability (re-running the exploit)
3. Verifying the application still starts and handles normal requests

### Library-Specific

Fixes use the correct API for the specific library version — not a generic pattern that might be correct for one version and incorrect for another.

## Fix Generation Flow

```python
async def generate_fix(
    finding: Finding,
    source_to_sink: DataFlowResult,
    exploit_result: ExploitResult
) -> GeneratedFix:
    
    prompt = f"""
    You are a security engineer fixing a confirmed {finding.vulnerability_class} vulnerability.
    
    Package: {finding.package}@{finding.version}
    Vulnerable file: {source_to_sink.sink}
    Vulnerable code path: {source_to_sink.path}
    Validation gap: {source_to_sink.validation_gaps}
    
    The exploit proof: {exploit_result.successful_payload}
    Impact achieved: {exploit_result.impact_achieved}
    
    Generate a minimal code fix that:
    1. Eliminates the specific validation gap identified
    2. Uses the correct API for {finding.package}@{finding.version}
    3. Returns early/denies requests that would exploit the vulnerability
    4. Does NOT change any other code
    5. Does NOT use regex-based denylists (use allowlists or normalization)
    
    Output format: unified diff (patch format)
    """
    
    fix_diff = await gemini_pro.generate(prompt, temperature=0.1)
    
    # Validate: does the fix actually work?
    validation = await sandbox.test_fix(
        package=finding.package,
        version=finding.version,
        fix_diff=fix_diff,
        exploit_payload=exploit_result.successful_payload
    )
    
    if not validation.fix_blocks_exploit:
        # Retry with feedback
        fix_diff = await generate_fix_with_feedback(finding, fix_diff, validation)
    
    return GeneratedFix(
        diff=fix_diff,
        explanation=generate_explanation(fix_diff),
        test_results=validation,
        confidence=validation.confidence
    )
```

## Fix Validation

Every generated fix is validated in the sandbox before being submitted:

| Test | Pass Condition |
|------|---------------|
| Application starts | Service responds to health check |
| Normal functionality | Existing test suite passes |
| Exploit blocked | PoC payload no longer achieves impact |
| No regression | No new errors introduced |
