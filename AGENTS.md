# Repository Instructions

## Purpose and Scope

This repository is for developing reusable skills and agents for FPR projects.

## Read the Relevant Guidance

- Read `README.md` for the repository overview.
- Before changing plugin metadata, capabilities, packaging, or helper scripts,
  read `docs/development.md`.
- When changing installation or distribution behavior, read
  `docs/installation.md` and verify commands against the installed Codex client's
  help and current official documentation.

## Repository Boundaries

- `plugin.json` is the authoritative plugin identity and version. Preserve the
  portable manifest format and put OpenAI-specific settings under
  `extensions.com.openai`.
- `.agents/plugins/marketplace.json` lists the single plugin. Keep its entry name
  consistent with the manifest and its source path inside the repository.
- `skills/` contains packaged workflow directories when a task adds them.
- `agents/` is reserved for agent source files. Confirm Codex's supported native
  agent registration mechanism before implementing agent integration; this
  directory alone does not register agents in consuming projects.
- `scripts/` contains maintenance helpers; `assets/` contains supporting resources.
- `.gitkeep` files preserve empty directories. Keep directories empty until the
  requested task introduces their capabilities.

Keep plugin resources self-contained so an installed copy can resolve its files.
Implement only the capabilities requested by the current task. Store credentials
and machine-specific settings outside tracked files.

## Editing and Validation

Inspect the working tree before editing and preserve existing user changes.
Follow the file conventions in `docs/development.md`. Update documentation when
behavior or structure changes, and record substantive changes in `CHANGELOG.md`.

Run validation appropriate to the change:

- For metadata changes, use the JSON checks in `docs/development.md`; verify the
  manifest and marketplace agree and referenced paths exist.
- For documentation changes, check relative links, line endings, and final
  newlines.
- For new scripts or capabilities, exercise meaningful behavior in the supported
  runtime. Static metadata validation does not prove installation or execution.
- Run `git diff --check`. Inspect new files separately because untracked files
  are not included in that command's checks.

When finished, report the changes, validation performed, and any unverified
behavior. Stage, commit, push, publish, or install only when the user requests
that action. An instruction to generate or edit files does not authorize those
additional operations.
