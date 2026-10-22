# HAST Kit

Agent skills for HAST tools. Start with **HAST GTM** (powered by Cutline): edit, preview and render
local videos with Codex, Claude Code and other compatible AI tools.

## Install

```sh
npx skills add itering/hast-kit -g
```

Choose the AI tools you use when prompted. To select HAST GTM explicitly:

```sh
npx skills add itering/hast-kit --skill hast-gtm -g
```

For an unattended installation into Codex and Claude Code:

```sh
npx skills add itering/hast-kit --skill hast-gtm -g -a codex -a claude-code -y
```

Reload your AI tool if the skill is not detected. Open the folder containing your
footage and ask:

> Use gtm video to set up the local editing environment, trim my video
> to 30 seconds, add a title, preview it, and export an MP4 to output/.

中文示例：

> 使用 gtm cutline，先检查并配置本地剪辑环境，把素材剪成 30 秒，加上标题，
> 先生成预览，再导出 MP4 到 output 文件夹，保留原始素材。

You can invoke the skill as `hast-gtm`, or ask for `gtm video` / `gtm cutline`.
The CLI command remains `cutline`. Skill selection depends on your AI tool; use
the explicit `hast-gtm` skill selector if natural-language selection misses it.

If you installed the previous `cutline` skill, remove that Skill using your
agent's skill management or `npx skills remove cutline -g`, then install again.
This removes only the old Skill, not the Cutline CLI or your video projects.

## What gets installed

The command installs Skill instructions. On first use, your AI tool follows the
[setup guide](skills/hast-gtm/references/install.md) to install the published
[Cutline CLI](https://www.npmjs.com/package/@hast/gtm-cutline) and check rendering
dependencies. Setup runs subject to your AI tool's normal execution permissions.

Cutline 0.4.1 requires Node.js 24+, FFmpeg/ffprobe and matching Playwright
Chromium for the recommended raster export. The alternative ffmpeg engine needs
libass for text/captions. See the [engine selection guide](skills/hast-gtm/references/workflow.md#choose-an-export-engine). You can also install the CLI yourself:

```sh
npm install -g @hast/gtm-cutline@0.4.1
cutline --help
```

Creative planning and animation components use HAST Claude Opus 5.5. The Skill
first generates a creative plan, then passes that plan to component generation.
Narration uses Gemini 3.8 Flash TTS. Only explicit user requests change the
creative model; failed calls never silently fall back.

Editing, project files, previews and video rendering stay on your computer.
Optional HAST component generation and narration use `cutline hast login` to
open HAST Account and authorize `models:invoke`. Credentials are stored using
macOS Keychain, Linux Secret Service, or Windows DPAPI. API-key import remains
available for automation. A compatible deployment must enable browser authorization;
this release has been verified against DEV, not production. Only the
content needed for those inference calls is sent to HAST. Never paste API keys
into a chat. See the [generation guide](skills/hast-gtm/references/hast.md).

## Included skills

| Skill | Purpose |
| --- | --- |
| [hast-gtm](skills/hast-gtm/SKILL.md) | Local video editing, HTML components, narration, preview and MP4 export |

This repository distributes Skill instructions and references. The CLI is
distributed separately through npm; HAST's service implementation is not included.

To refresh the installed Skill, rerun the installation command above.
