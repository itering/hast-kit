# Install Cutline

The Skill supplies instructions; the CLI compiles, previews and renders locally.
When the user asks to edit with Cutline, check the local environment and complete
these setup steps as needed, following the host tool's execution permissions.
Preserve existing installations that already work.

## Install the CLI

Check `node --version` and `cutline --help`. Cutline requires Node.js 24 or later.
If Node is missing or older, use the user's existing Node version manager or
platform installation method. Do not replace an unrelated project toolchain.
Install the published CLI when it is missing:

```sh
npm install --global @hast/gtm-cutline@0.1.0
cutline --help
cutline doctor
```

If global installation is unavailable, use the same CLI through npm exec:

```sh
npm exec --yes --package=@hast/gtm-cutline@0.1.0 -- cutline --help
```

Use that prefix for subsequent Cutline commands in this case. Do not use sudo
or change global npm ownership just to work around an installation error.

## Rendering dependencies

`cutline doctor` reports prerequisites as JSON and exits nonzero when required
capabilities are missing. Inspect the result and install only missing tools.
Rendering requires FFmpeg and ffprobe; the default FFmpeg text renderer also
requires libass. Use the platform's available package manager and verify the
actual capabilities with doctor after installation.

Cutline 0.1.0 pins Playwright 1.62.1. Install its matching Chromium:

```sh
npm exec --yes --package=playwright@1.62.1 -- playwright install chromium
cutline doctor
```

For other Cutline versions, read the exact Playwright dependency from the
installed package's package.json instead of assuming the same browser version.

## Install or update the Skill

The recommended public installation is:

```sh
npx skills add itering/hast-kit -g
```

Select the AI tools to use when prompted. To install only Cutline for Codex and
Claude Code without prompts:

```sh
npx skills add itering/hast-kit --skill cutline -g -a codex -a claude-code -y
```

This installs instructions, not the CLI or rendering dependencies. If this Skill
is already loaded, do not reinstall it. Reload the host tool if a newly installed
Skill is not detected. To refresh it later, run the installation command again.

## Start editing

```sh
cutline init my-video
```

Replace the starter's placeholder footage with the user's local media, then
follow [the local workflow](workflow.md) to validate, preview and export.
Keep original footage intact and write exports separately.

Hast generation is optional and separately authenticated. Local editing needs
no Hast key. For generated components or narration, read [Hast generation](hast.md).
Use the API endpoint supplied for the user's deployment; do not guess a production
endpoint. Generation consumes Hast model usage; Codex subscriptions do not include it.
