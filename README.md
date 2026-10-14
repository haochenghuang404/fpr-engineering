# FPR Engineering

A single Codex plugin for reusable FPR engineering workflows.

The repository currently contains plugin metadata, a one-plugin marketplace,
documentation, and empty extension directories. No skills, agents, MCP servers,
or lifecycle hooks are implemented.

## Repository layout

```text
fpr-engineering/
├── plugin.json                         # Plugin identity and Codex presentation
├── AGENTS.md                           # Codex repository maintenance instructions
├── .agents/plugins/marketplace.json    # One-plugin distribution catalog
├── skills/                             # Reserved for future skills
├── agents/                             # Reserved for future agent source files
├── scripts/                            # Reserved for future maintenance helpers
├── assets/                             # Reserved for future plugin assets
├── docs/
│   ├── installation.md
│   └── development.md
├── CHANGELOG.md
└── .gitignore
```

The repository root is also the plugin root. The marketplace entry points to
`./`, resolved from the repository root. Empty directories contain `.gitkeep`
files so Git can preserve the scaffold.

## Codex support

`plugin.json` uses the portable Agent Plugins 1.0 format. OpenAI-specific
presentation metadata lives under `extensions.com.openai`. Codex discovers
future packaged skills from the root `skills/` directory.

`agents/` is reserved for source files. It is not a native Codex agent discovery
directory, and installing the plugin does not register files from it as named
agents. Agent registration will be designed when agent functionality is added.

## Getting started

See [Installation](docs/installation.md) for local and private-repository setup.
Installing this scaffold provides no executable workflows yet.

See [Repository Instructions](AGENTS.md) for Codex maintenance guidance.

See [Development](docs/development.md) for repository conventions and metadata
validation. All maintained file content is written in English.

## References

- [Package a Codex plugin](https://developers.openai.com/plugins/build/plugins)
- [Codex skills](https://learn.chatgpt.com/docs/build-skills)
- [Codex custom agents](https://learn.chatgpt.com/docs/agent-configuration/subagents)
