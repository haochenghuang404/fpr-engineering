# Installation

This repository is a Codex-only plugin scaffold. It does not provide any skills
or agents yet. Installation is optional during initialization.

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

After the scaffold has been committed and pushed, register the Git source:

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

## Future agents

Codex currently discovers native custom agents from `~/.codex/agents/` for a
user or `.codex/agents/` for a project. The reserved `agents/` directory in this
plugin does not register native agents automatically. This scaffold includes
no agent definitions or registration scripts.
