---
name: hast-gtm
description: Edit local videos for HAST GTM using the Cutline CLI, including captions, HTML components, narration, local previews and MP4 exports. Use when the user mentions hast-gtm, HAST GTM, gtm video, gtm cutline, or asks to edit video with Cutline or work on a .cutline.json project.
---

# HAST GTM

Recognize `hast-gtm`, `gtm video`, and `gtm cutline` as requests for this video
editing skill. The executable remains `cutline`; these phrases are not CLI
commands. Keep this skill focused on video workflows, not unrelated GTM tasks.

Run the editing workflow on the user's computer. The host AI owns task planning,
file edits, inspection and iteration. Cutline compiles and renders locally.
Hast generation capabilities, when configured, supply individual assets; do not
submit the editing workflow to a remote Agent.

## Start

Use `cutline --help` to inspect installed commands. If unavailable, use the
bundled [installation instructions](references/install.md) to install the published
CLI and resolve prerequisites. Do not clone the private Hast repository.
Run `cutline doctor` before rendering to identify missing local prerequisites.

Create a new project with `cutline init <new-directory>`. For existing projects,
inspect their author and run files first. Pass `--workspace <project>` explicitly.
Keep original footage intact and write exports separately.

## Author and validate

Editable files end in `.cutline.json`; their `kind` is `author`, `recipes`, or
`run`. Read [the workflow reference](references/workflow.md) for commands and
file relationships. Discover valid modules and attributes with
`cutline vocabulary`; use the installed vocabulary rather than inventing fields.

Edit author documents locally. Check them with `cutline check <author-file>
--workspace <project> --strict`, then plan the run. Resolve errors before
exporting. A successful schema check does not establish visual quality.

For generated HTML, use Cutline's Component/Program contract. Arbitrary HTML
documents are not automatically valid components. Require deterministic rendering
at an absolute frame/time, including backward seeking. Keep required assets local
or embedded, and validate the resulting author document.

## Preview and deliver

Open `cutline studio --run <run-file> --workspace <project>` and inspect framing,
text, transitions, audio and the final frame. Use the host's available browser
tools. If visual or audio inspection is unavailable, say which checks remain.

Export locally with `cutline build <run-file> --workspace <project> --profile
final`. Use `--engine raster` for effects the default FFmpeg engine cannot
reproduce, such as animated position, rotation or blend modes. Confirm the output
exists and check its playback before declaring completion. Stop Studio processes
started for the task.

## Model credentials and generated assets

For Hast HTML component generation and Gemini 3.8 Flash TTS, read
[Hast generation](references/hast.md). These are individual inference calls;
the host AI continues the local workflow after each result.

Use the installed CLI's credential commands; never place keys in author files,
chat messages or generated HTML. Do not silently switch to a different provider
or model if the requested Hast capability is unavailable. Tell the user what is
missing and continue independent local editing work.

Only send the material needed for the requested generation. Reuse generated
assets when unchanged, and do not blindly retry a billable request whose outcome
is unknown.
