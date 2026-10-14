# FPR Engineering

A single Codex plugin for reusable FPR engineering workflows.

The plugin includes `deep-review`, a four-perspective adversarial code review
workflow, its agent role sources, and supporting security references.
Project-specific conventions come from the FPR repository being reviewed.
No MCP servers or lifecycle hooks are included.

## Repository layout

```text
fpr-engineering/
├── plugin.json                         # Plugin identity and Codex presentation
├── AGENTS.md                           # Codex repository maintenance instructions
├── .agents/plugins/marketplace.json    # One-plugin distribution catalog
├── skills/deep-review/                  # Review workflow and usage
├── agents/                             # Four review role sources
│   └── security-knowledge/              # Vulnerability patterns and controls
├── scripts/                            # Reserved for future maintenance helpers
├── assets/                             # Reserved for future plugin assets
├── docs/
│   ├── installation.md
│   └── development.md
├── CHANGELOG.md
└── .gitignore
```

The repository root is also the plugin root. The marketplace entry points to
`./`, resolved from the repository root. Empty extension directories contain
`.gitkeep` files so Git can preserve them.

## Codex support

`plugin.json` uses the portable Agent Plugins 1.0 format. OpenAI-specific
presentation metadata lives under `extensions.com.openai`. Codex discovers
packaged skills from the root `skills/` directory.

`agents/` contains role prompts loaded explicitly by `deep-review` into
independent subagents. It is not a native Codex agent discovery directory.
The workflow resolves all role and reference paths from the installed plugin
root, so it also works when the reviewed project is a different repository.

## Getting started

See [Installation](docs/installation.md) for local and private-repository setup.
See [Deep Review](skills/deep-review/README.md) for supported review targets,
the four perspectives, and standalone use of individual role prompts.

See [Repository Instructions](AGENTS.md) for Codex maintenance guidance.

See [Development](docs/development.md) for repository conventions and metadata
validation. All maintained file content is written in English.

## References

- [Package a Codex plugin](https://developers.openai.com/plugins/build/plugins)
- [Codex skills](https://learn.chatgpt.com/docs/build-skills)
- [Codex custom agents](https://learn.chatgpt.com/docs/agent-configuration/subagents)
