# Chapter 9 – Governance, Testing, and Observability

## 9.1 Responsible AI and Policy

- Document Copilot use in PRs and ADRs.  
- Enable secret scanning and dependency/License checks (SBOM).

## 9.2 Observability Baseline

**Prompt:**
```text
Add Micrometer/OpenTelemetry tracing to key service methods and outbound calls.
```

**Excerpt (illustrative):**
```java
var span = tracer.spanBuilder("placeOrder").startSpan();
try { /* service logic */ } finally { span.end(); }
```

## 9.3 CI/CD Hooks

- Build, test, and static analysis on every PR.  
- Optional: security scans and SCA reports as artefacts.

> 💡 **Copilot Tip:** Ask Copilot to generate a minimal GitHub Actions workflow, then tailor to your environment.
