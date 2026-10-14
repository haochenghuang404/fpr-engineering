# Deep Review

Deep adversarial review of FPR code changes through four independent
perspectives:

| Perspective | Focus |
| --- | --- |
| Advocate | Author intent, design rationale, trust boundary guarantees, and false-positive defense |
| Skeptic | Reproducible bugs, edge cases, error paths, concurrency, and test gaps |
| Architect | System impact, coupling, abstractions, technical debt, and direction |
| Security | Authorization, data isolation, injection, sensitive data, and exploit paths |

Use this workflow for complex reviews. Each perspective runs in its own
subagent, reads the same captured context, and cites evidence. The orchestrator
shows the raw analyses and reconciles agreement and disputes into one report.

## Usage

```text
$deep-review                    # Uncommitted local changes
$deep-review <PR-ID-or-URL>     # A pull request
$deep-review <commit>           # A commit
$deep-review <file1> <file2>    # Selected files
```

Use Codex's skill picker to select this plugin's `deep-review` skill. Specify the
target FPR project when the workspace contains multiple repositories.

Project architecture, coding rules, and test conventions come from the reviewed
project's instructions and relevant documentation. The review reports findings
without changing application files or Git state.

## Workflow

1. Gather the diff, reviewed file versions, project instructions, and relevant
   callers, callees, configuration, and tests into a unique temporary snapshot.
2. Run Advocate, Skeptic, Architect, and Security independently using the
   packaged role sources. Run concurrently within the available worker limit.
3. Show the raw perspectives, merge corroborated findings, retain unresolved
   disputes, and recommend prioritized actions.
4. Delete the temporary context after all reviewers finish, including on
   failure or abort.

Snapshots live outside the reviewed repository. For large changes, split the
review or explicitly document summarized coverage. Label pre-existing findings
separately from introduced issues.

## Priorities and Evidence

- **Critical**: Must fix now - corruption, crash, security in normal usage.
- **High**: Should fix before merge - bug exists under specific conditions.
- **Medium**: Fix soon - correct but fragile, maintenance risk.
- **Low**: Nice to have - minor improvement, not urgent.

Evidence beats assertion. Skeptic owns correctness, Architect owns direction,
and Security owns security concerns unless a specific mitigation refutes the
finding. Unresolved evidence remains **Disputed**.

## Packaged Agents

The plugin's `agents/` directory contains Markdown role sources loaded
explicitly by the skill. Plugin installation does not register native named
Codex agents. To use one perspective on its own, ask Codex to read that role
source and review a specified target; each source supports standalone mode.

- [Advocate](../../agents/DeepReviewAdvocate.agent.md)
- [Skeptic](../../agents/DeepReviewSkeptic.agent.md)
- [Architect](../../agents/DeepReviewArchitect.agent.md)
- [Security](../../agents/SecurityReview.agent.md)
- [Vulnerability Patterns](../../agents/security-knowledge/vulnerability-patterns.md)
- [Security Controls](../../agents/security-knowledge/security-controls.md)

See [SKILL.md](SKILL.md) for orchestration, failure handling, conflict rules,
the context schema, and the final report format.
