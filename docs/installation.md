# Installation

This repository provides a Codex plugin with the `deep-review` skill,
four review role sources, and supporting security references.

## Install from a local checkout

Run the following commands from the repository root:

```sh
codex plugin marketplace add .
codex plugin add fpr-engineering@fpr-engineering
```

The first command registers the marketplace. The second installs its single
plugin. The plugin selector uses the plugin name followed by the marketplace
name; both are currently `fpr-engineering`.

The commands above are supported by Codex CLI 0.162.1. Check the installed
client's help when using a different version:

```sh
codex plugin --help
codex plugin marketplace add --help
codex plugin add --help
```

In the desktop app, open the Plugins Directory, select the FPR Engineering
marketplace, and install FPR Engineering. Restart or refresh the app if the
newly registered marketplace is not visible.

## Install from the private repository

After the desired version has been committed and pushed, register the Git source:

```sh
codex plugin marketplace add haochenghuang404/fpr-engineering
codex plugin add fpr-engineering@fpr-engineering
```

The machine running Codex needs Git authentication with read access to the
private repository. Keep credentials outside the plugin's files.

## Refresh a Git marketplace

Refresh a registered marketplace after a new version is pushed:

```sh
codex plugin marketplace upgrade fpr-engineering
```

Then use the client's supported update or reinstall flow for the plugin.
Maintain source files in this repository; installed plugin copies are caches.

## Review agents

Codex currently discovers native custom agents from `~/.codex/agents/` for a
user or `.codex/agents/` for a project, using standalone TOML definitions.
See the [custom-agent documentation](https://learn.chatgpt.com/docs/agent-configuration/subagents).

The plugin's `agents/` directory holds Markdown role prompts. The `deep-review`
skill reads them and supplies their instructions to independent subagents, so
no native agent registration or configuration edits are required.
After installing or refreshing the plugin, select `deep-review` in the skill
picker and identify the FPR project and review target. See
[Deep Review usage](../skills/deep-review/README.md) for details.
