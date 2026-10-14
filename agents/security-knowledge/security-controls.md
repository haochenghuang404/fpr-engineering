# Security Controls

Use this reference when reviewing authentication, transport, network exposure,
credential handling, logging, or deployment changes in an FPR project.
Apply documented project requirements and verify compensating controls.
These checks are not a claim that the project follows a specific compliance
program.

| Control | Pattern to investigate | Evidence needed |
| --- | --- | --- |
| Authentication | Anonymous access to a sensitive route or operation | Reachable endpoint and missing effective authentication |
| Authorization | Trusted client IDs or client-side checks replace server permissions | Unauthorized principal reaching a protected resource or action |
| Token validation | Disabled signature, issuer, audience, expiry, or algorithm checks | Accepted attacker-controlled token and applicable trust requirement |
| Certificate validation | Trust-all certificate manager, hostname verifier, or permissive callback | Sensitive connection accepting an untrusted peer |
| Transport protection | Cleartext sensitive traffic or weakened protocol configuration | Effective listener/client settings and data crossing the connection |
| Service identity | Bypass of the documented service authentication mechanism | New call path and absent equivalent control |
| Network exposure | New public listener or permissive network policy | Reachability and ineffective access control for a sensitive service |
| Credential storage | Hardcoded passwords, tokens, signing keys, or embedded connection credentials | Real credential material or a deployed secret flow |
| Logging and tracing | Secrets or personal data in logs, errors, spans, or request dumps | Sensitive value, unredacted sink, and unauthorized readership |
| Deployment separation | Development auth/debug bypass enters production configuration | Configuration resolution or deployment path that activates the bypass |

## Targeted Scanning

Use searches to locate candidate sinks, then inspect their call paths and
configuration. In Java/Kotlin projects, useful terms may include:

```text
TrustManager
X509TrustManager
HostnameVerifier
NoopHostnameVerifier
checkServerTrusted
verify(
ProcessBuilder
Runtime.getRuntime
createStatement
executeQuery
clientSecret
password
accessToken
Authorization
```

These terms are discovery aids, not automatic findings. Use equivalent APIs in
other languages. Do not print credential values while gathering evidence.

## Suppressions and Compensating Controls

- Inspect documented suppressions and security exceptions, including their
  applicability to the changed path.
- A suppression comment does not itself prove a mitigation. Verify the control
  it references and explain whether it still holds.
- Distinguish a secret-manager key/name from the secret value it resolves.
- A private listener or test-only bypass needs a supported path to deployed
  exposure before becoming a vulnerability finding.
- When a project requirement is unavailable, record that limitation instead of
  inventing an organization-specific policy.
