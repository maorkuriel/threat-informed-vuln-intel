# Source-to-Sink Analysis

!!! abstract "Overview"
    Source-to-Sink analysis is the process of tracing untrusted attacker-controlled input from where it enters an application (source) through the code to where it reaches a dangerous operation (sink). TIVI automates this across any language and vulnerability class.

## Concept

```
ATTACKER INPUT                           DANGEROUS OPERATION
     │                                          │
[Source]  ──── data flows through code ────>  [Sink]
     │                                          │
  HTTP param                            File read / DB query /
  File upload                           Command exec / Deserialize
  Config value
  Network packet
```

A vulnerability exists when:
1. Attacker-controlled data reaches a dangerous operation
2. Without sufficient validation or sanitization in between

## Supported Vulnerability Classes

TIVI's Source-to-Sink analyzer handles any class of vulnerability by configuring the appropriate source and sink patterns:

| Vulnerability Class | Common Sources | Common Sinks |
|--------------------|---------------|-------------|
| Path Traversal | URL path, file upload name | `open()`, `readFile()`, `send_file()` |
| SQL Injection | Query parameters, form fields | `cursor.execute()`, `db.query()` |
| Command Injection | CLI args, env vars, user input | `exec()`, `subprocess.run()`, `os.system()` |
| SSRF | URLs from user input | HTTP client calls, socket connections |
| Deserialization | Serialized data from network | `pickle.loads()`, `yaml.load()`, `ObjectInputStream` |
| XSS | User-supplied strings | HTML template rendering, `innerHTML` |
| XXE | XML input | XML parsers without entity restrictions |

## Analysis Process

```python
# Simplified representation of the analysis flow
class SourceToSinkAnalyzer:
    
    def analyze(self, package_path: str, advisory: AdvisoryContext) -> DataFlowResult:
        # 1. Build call graph from all entry points
        entry_points = self.find_entry_points(package_path, advisory.attack_vector)
        
        # 2. Identify candidate sinks based on vulnerability class
        sinks = self.find_sinks(package_path, advisory.vulnerability_class)
        
        # 3. Trace paths from each entry point to each sink
        paths = []
        for entry in entry_points:
            for sink in sinks:
                path = self.trace_path(entry, sink, package_path)
                if path:
                    paths.append(path)
        
        # 4. Score paths by exploitability (fewer validation hops = higher score)
        ranked = self.rank_by_exploitability(paths)
        
        # 5. Return highest-confidence path with gap analysis
        return self.build_result(ranked[0]) if ranked else None
    
    def find_validation_gaps(self, path: CallPath) -> list[ValidationGap]:
        """Identify missing or bypassable input validation along the data flow path."""
        gaps = []
        for node in path.nodes:
            validation = self.detect_validation(node)
            if validation.is_absent:
                gaps.append(ValidationGap(location=node, type="missing"))
            elif validation.is_bypassable:
                gaps.append(ValidationGap(location=node, type="bypassable",
                                          bypass_technique=validation.bypass))
        return gaps
```

## Output Example

```json
{
  "entry_point": "GET /files/:filename",
  "sink": "src/handlers/files.js:47 — fs.readFile(filePath)",
  "path": [
    "routes/files.js:12 — router.get('/:filename', handler)",
    "handlers/files.js:38 — const filePath = path.join(baseDir, req.params.filename)",
    "handlers/files.js:47 — fs.readFile(filePath, callback)"
  ],
  "validation_gaps": [
    {
      "location": "handlers/files.js:38",
      "type": "missing",
      "description": "No normalization of req.params.filename before path.join"
    }
  ],
  "attack_surface": "req.params.filename — fully attacker-controlled, no encoding applied"
}
```

!!! tip
    The Source-to-Sink result directly informs the Payload Generator — knowing exactly what input is attacker-controlled and where it reaches the sink allows the generator to craft precisely targeted payloads rather than generic templates.
