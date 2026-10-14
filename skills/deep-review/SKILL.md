---
name: deep-review
description: Deep adversarial review of FPR code changes using Advocate, Skeptic, Architect, and Security perspectives. Use for local changes, pull requests, commits, or selected files that need a thorough review.
metadata:
  version: "1.0.0"
---

# Deep Review

Perform thorough code review using four independent agents with opposing
mindsets:

- **Advocate** - Why is this correct? Design rationale, trust boundaries, and
  false-positive defense.
- **Skeptic** - How can I break this? Bugs, edge cases, and invariant gaps.
- **Architect** - Is this the right direction? System impact, coupling, and debt.
- **Security** - How can I exploit this? Authorization, injection, sensitive
  data, and trust boundary violations.

Their disagreement surfaces issues. Their agreement signals confidence.

## Usage

```text
$deep-review                    # Review uncommitted local changes
$deep-review <PR-ID-or-URL>     # Review a pull request
$deep-review <commit>           # Review a specific commit
$deep-review <file1> <file2>    # Review specific files
```

Run the review against the FPR project selected by the user. Resolve project
conventions from that project's instructions and documentation. The installed
plugin directory holds this workflow and its agents, not the reviewed code.
Report findings and recommendations; leave application files and Git state
unchanged unless the user separately requests fixes. Posting review comments
requires an explicit request.

## Workflow

```text
Phase 1: GATHER CONTEXT (orchestrator)
    Collect the changes, project instructions, and relevant supporting code.
    Write one shared snapshot to a unique temporary context file.

Phase 2: PARALLEL ANALYSIS (four independent agents)
    Verify the context file and packaged role sources.
    Each reviewer reads the same snapshot using its own role instructions.

Phase 3: SYNTHESIS (orchestrator)
    Present the raw perspectives and reconcile their findings.
    Delete the temporary context file on every exit path.
```

## Phase 1: Gather Context

### 1.1 Identify the Project and Changes

Resolve the target project's root and the requested review scope:

- Local changes: inspect staged, unstaged, and untracked files.
- PR/MR: use available repository tools or a local checkout to obtain the exact
  base and head versions, description, diff, and existing feedback.
- Commit: inspect its diff against the relevant parent and read files at that
  commit, even when it differs from the current checkout.
- File list: review the specified files as the target set.

Use project-specific tools documented in the target project's `AGENTS.md` or
`CLAUDE.md` when available. Otherwise use `git diff`, `git show`, `git log`,
and file reads. Report inaccessible review targets instead of substituting
another scope.

Read the target project's repository and applicable component instructions.
Capture architecture notes and test conventions relevant to the change.
Shared reviewers must follow the project's actual language and frameworks,
not assume one universal FPR module layout or coding pattern.

### 1.2 Fetch Content

Capture the diff and full new contents of each changed file from the selected
review version. Include old contents for deleted files and the previous path
for renames. Capture relevant callers, callees, contracts, configuration, and
tests at the same version so all reviewers can trace the same execution paths.
Preserve repository-relative paths and original line numbers for citations.

### 1.3 Write Context File

Create a unique path outside the repository:

```bash
CONTEXT_FILE="$(mktemp "${TMPDIR:-/tmp}/deep-review-context.XXXXXX")"
```

Use this structure, omitting optional fields that do not apply:

```yaml
review:
  type: pr | local | commit | files
  id: <identifier if applicable>
  title: <summary>
  description: <details>
  base: <base commit if applicable>
  head: <head commit if applicable>

project:
  root: <absolute path of the reviewed FPR project>
  instructions:
    - path: <repository-relative instruction path>
      content: |
        <applicable project instructions>
  architecture_notes: |
    <relevant project context, with source references>
  test_commands:
    - <command documented by the reviewed project>

changed_files:
  - path: <repository-relative path>
    previous_path: <old path for a rename>
    action: add | edit | delete | rename
    diff: |
      <diff with original hunk locations>
    content: |
      <full file content at the reviewed version>
    old_content: |
      <deleted file content, when applicable>

supporting_files:
  - path: <repository-relative path>
    content: |
      <relevant unchanged code, contracts, tests, or documentation>

existing_feedback:
  - author: <who>
    location: <file:line or general>
    comment: <text>

observations:
  - <known limitations or other notable facts>
```

For changes exceeding roughly 50 KB of captured content, summarize
less-critical files with an explicit coverage note or split into smaller reviews.
Keep security-sensitive code and relevant call chains available in full.

### 1.4 Verify Context

Confirm the file exists and contains the intended snapshot before dispatch.
Resolve the four role files below relative to this `SKILL.md`, including when
running from an installed plugin copy. Convert those paths to absolute paths
for child prompts. Verify the security knowledge files referenced by the
Security role are present too. If a required resource is missing, report the
missing path before starting analysis.

## Phase 2: Parallel Analysis

### 2.1 Dispatch Four Reviewers

The role sources are packaged in the plugin's root `agents/` directory:

| Role | Name | Instruction source |
| --- | --- | --- |
| Advocate | DeepReviewAdvocate | [DeepReviewAdvocate.agent.md](../../agents/DeepReviewAdvocate.agent.md) |
| Skeptic | DeepReviewSkeptic | [DeepReviewSkeptic.agent.md](../../agents/DeepReviewSkeptic.agent.md) |
| Architect | DeepReviewArchitect | [DeepReviewArchitect.agent.md](../../agents/DeepReviewArchitect.agent.md) |
| Security | SecurityReview | [SecurityReview.agent.md](../../agents/SecurityReview.agent.md) |

These files contain role prompts; installing the plugin does not register them
as native named Codex agents. Read each role source and use Codex's available
subagent tools to dispatch an independent reviewer with those instructions.
Use fresh child context, such as `fork_turns="none"` when supported, so reviewers
do not inherit the conversation or other reviewers' conclusions.

Launch all four concurrently when capacity permits. If fewer worker slots are
available, queue the remaining perspective as a slot becomes free. Every
perspective must still analyze the same context file.

Give each child the role instructions and a prompt in this form:

```text
You are the <ADVOCATE / SKEPTIC / ARCHITECT / SECURITY REVIEWER>
in a four-perspective adversarial code review of an FPR project.
Follow the role instructions supplied with this task.
Review the shared context file: <absolute context-file path>.
The reviewed project root is: <absolute target-project path>.
The plugin root is: <absolute installed-plugin path>.
Use the shared snapshot as your source of changed code and project conventions.
Return your role's analysis with file:line evidence and the
Critical/High/Medium/Low priority scale.
Keep the review read-only and do not delegate further.
```

Security focuses only on security; other perspectives cover intent, correctness,
and direction. Keep the context file until all active reviewers finish.

### 2.2 Handle Failures

If a reviewer fails or times out, offer these choices:

- **Re-trigger** - run only that perspective again against the same snapshot.
- **Proceed without** - synthesize the available results and name the missing
  perspective and resulting coverage gap.
- **Abort** - stop the review and clean up.

If subagent tools are unavailable, report the missing capability. Do not label
a single-agent assessment as a completed four-agent deep review.

## Phase 3: Synthesis

### 3.1 Present Raw Perspectives

Show each reviewer's analysis in full before synthesis so the user can inspect
the evidence and overrule the interpretation.

### 3.2 Agreement Analysis

Identify independently corroborated findings, consolidate duplicates, and
include high-confidence findings in the final review. Distinguish newly
introduced issues from pre-existing problems in surrounding code.

### 3.3 Conflict Resolution

- Skeptic finds a bug and Advocate defends it: the defense must cite specific
  evidence that refutes the failing path; otherwise keep the finding.
- Advocate says intentional and Skeptic demonstrates a reproducible failure:
  report the bug regardless of intent.
- Architect and Skeptic disagree on correctness priority: use Skeptic's
  assessment for correctness concerns.
- Architect and Advocate disagree on direction: use Architect's assessment
  for architectural concerns.
- Security finds a vulnerability and Advocate defends it: retain the finding
  unless Advocate cites a specific mitigation at `file:line`.
- Security and Skeptic flag the same issue: use the higher supported priority;
  overlapping evidence increases confidence.
- Evidence is inconclusive: mark the issue **Disputed** for the user to decide.

Evidence beats assertion. Keep unresolved opposing evidence visible.

### 3.4 Final Review

```markdown
## Deep Review: <title>

### Summary
<1-2 sentence overview and review scope>

### Perspectives

**Author's Intent** (Advocate)
<key defenses and rationale>

**Risk Analysis** (Skeptic)
<bugs, edge cases, and concerns>

**Architectural Impact** (Architect)
<patterns, coupling, debt, and direction>

**Security Assessment** (Security)
<vulnerabilities, trust boundaries, and attack surface>

### Consolidated Findings

- <issue and file:line evidence>
  - **Priority**: Critical/High/Medium/Low
  - **Origin**: Introduced/Pre-existing
  - **Advocate**: <view>
  - **Skeptic**: <view>
  - **Architect**: <view>
  - **Security**: <view>

### Disputed (if any)

- <issue>
  - **Advocate**: <defense>
  - **Skeptic/Architect/Security**: <concern>
  - **Resolution**: User to decide

### Recommendations
<prioritized actions>

### Follow-up Items
<non-blocking concerns worth tracking>

### Coverage and Limitations
<missing perspectives, inaccessible files, summaries, or unverified paths>
```

### 3.5 Cleanup (Always)

Delete the context file after reviewers finish, on success, failure, or abort:

```bash
rm -f "$CONTEXT_FILE"
```

The snapshot may contain sensitive code. On abort, stop active reviewers before
deleting their shared input.

## Notes

- This workflow uses four reviewers; reserve it for complex reviews.
- Include useful pre-existing findings, clearly labeled.
- Report both sides when evidence remains contradictory.
