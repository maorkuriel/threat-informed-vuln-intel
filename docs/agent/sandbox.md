# Sandbox Execution

!!! abstract "Overview"
    TIVI executes all exploit validation inside ephemeral, isolated Docker containers. This makes exploit testing a zero-risk activity — no production systems are ever touched, and every execution is fully auditable.

## Safe-by-Design Principles

| Principle | Implementation |
|-----------|---------------|
| **Network isolation** | Containers run with `--network none` — no outbound connections |
| **Ephemeral filesystem** | Fresh container per execution, destroyed immediately after |
| **Resource limits** | CPU, memory, and execution time caps prevent runaway processes |
| **No privilege escalation** | Containers run as unprivileged user, `--security-opt no-new-privileges` |
| **Audit logging** | All executions logged with input, output, timestamps, and container ID |

## Execution Flow

```bash
# TIVI sandbox execution (simplified)

# 1. Build a minimal container with the vulnerable package installed
docker build \
  --build-arg PACKAGE="<package>@<affected_version>" \
  --build-arg RUNTIME="node|python|java|..." \
  -t tivi-sandbox:<execution_id> \
  -f sandbox/Dockerfile.template .

# 2. Start the application in the container
docker run -d \
  --name sandbox-<execution_id> \
  --network bridge \          # Isolated network — no external access
  --memory 512m \
  --cpus 0.5 \
  --read-only \
  --security-opt no-new-privileges \
  tivi-sandbox:<execution_id>

# 3. Execute payloads against the running application
for payload in payloads:
    response = send_payload(payload, container_port)
    result = interpret_response(response, expected_impact_indicator)

# 4. Destroy container and image — no trace left
docker rm -f sandbox-<execution_id>
docker rmi tivi-sandbox:<execution_id>
```

## Runtime Detection

TIVI supports multiple application runtimes and detects the correct startup method automatically:

```python
class RuntimeDetector:
    STARTUP_STRATEGIES = [
        NodeJSStrategy(),     # node index.js, npm start, yarn start
        PythonStrategy(),     # python app.py, flask run, uvicorn
        JavaStrategy(),       # java -jar app.jar, mvn spring-boot:run
        RubyStrategy(),       # ruby app.rb, rails server
        GoStrategy(),         # go run main.go, ./app
        PHPStrategy(),        # php -S localhost:8080
    ]
    
    def detect_and_start(self, package_path: str) -> int:
        """Returns the port the application started on."""
        for strategy in self.STARTUP_STRATEGIES:
            if strategy.matches(package_path):
                port = strategy.start(package_path)
                if port:
                    return port
        raise RuntimeError("Could not determine application startup method")
```

## Execution Results Schema

```python
@dataclass
class SandboxResult:
    execution_id: str
    timestamp: datetime
    duration_seconds: float
    payloads_tested: int
    successful_payloads: list[str]
    failed_payloads: list[str]
    impact_achieved: str | None         # e.g., "arbitrary file read confirmed"
    impact_evidence: str | None         # actual output proving impact
    container_id: str                   # for audit trail
    exit_reason: str                    # "completed", "timeout", "error"
```

## Audit Trail

Every sandbox execution is logged immutably:

```json
{
  "execution_id": "exec-2026-04-06-a3f91b",
  "finding_id": "finding-uuid",
  "container_id": "sha256:abc123...",
  "started_at": "2026-04-06T10:23:11.442Z",
  "completed_at": "2026-04-06T10:26:24.118Z",
  "duration_seconds": 193.0,
  "payloads_tested": 24,
  "result": "CONFIRMED",
  "impact": "Arbitrary file read without authentication",
  "evidence_hash": "sha256:def456..."
}
```

!!! warning "Enterprise Deployment Note"
    In enterprise environments, TIVI can be configured to run sandboxes on a dedicated execution cluster completely isolated from production networks. The orchestrator and sandbox infrastructure should run in separate network segments with no shared credentials.
