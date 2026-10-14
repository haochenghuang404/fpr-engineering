# Development

## Scope

Maintain one Codex plugin in this repository. Keep plugin files at the
repository root and use `plugin.json` as the authoritative identity and version.
The marketplace catalog lists that plugin once and points to `./`.

## Extension directories

- `skills/`: packaged workflows containing a `SKILL.md` entry point.
- `agents/`: review role sources and their supporting references. The
  `deep-review` workflow loads these instructions explicitly into subagents.
- `scripts/`: future maintenance or packaging helpers.
- `assets/`: future icons, templates, and other supporting resources.

The `deep-review` capability occupies `skills/deep-review/` and `agents/`.
Empty extension directories retain their `.gitkeep` files.

## Deep Review resources

Keep the four role sources and security references inside the plugin root.
Resolve their paths relative to the skill or role file, then pass absolute
paths to reviewers. Resolve application architecture, instructions, and test
commands from the target FPR project.

The `.agent.md` files are Markdown role prompts with name/description
frontmatter. They are not native Codex custom-agent registration files.
The skill uses available subagent tools with fresh context and these prompts;
it does not require user or project configuration changes.

When changing this workflow, validate the skill frontmatter, every packaged
reference, and the four-perspective context/analysis/synthesis flow. Verify
resources can also be resolved from a relocated plugin copy. Test installed
execution in a supported client when installation is explicitly requested.

## Project boundaries

Shared workflows belong in the plugin. Project-specific architecture, test
commands, service names, monitoring endpoints, and runbooks belong in each
project's own instructions and documentation. Resolve that context from the
project being worked on. Keep shared guidance independent of any one project's
implementation.

## File conventions

- Write documentation, configuration, comments, and future instructions in English.
- Use UTF-8 text, LF line endings, and a final newline for nonempty text files.
- Format JSON with two-space indentation.
- Keep referenced resources inside the distributable plugin root.
- Store credentials and machine-specific settings outside the repository.
- Leave empty `.gitkeep` files as zero-byte placeholders.

## Validate metadata

Run from the repository root:

```sh
python3 -m json.tool plugin.json > /dev/null
python3 -m json.tool .agents/plugins/marketplace.json > /dev/null
git diff --check
```

These commands check JSON syntax and tracked-diff whitespace. Also confirm that
the marketplace entry name matches the plugin manifest name and that its source
path resolves to the directory containing `plugin.json`.

Static metadata checks do not prove installation or runtime behavior. Verify
those in a supported Codex client when a capability is implemented.

## Versioning

Use semantic versions in `plugin.json`. Record changes in `CHANGELOG.md`, keeping
unreleased work under `Unreleased` until a release is prepared. Preserve the
plugin name and marketplace name across releases so installation selectors stay
stable.
