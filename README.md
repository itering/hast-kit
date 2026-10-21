# HAST Kit

Agent skills for HAST tools. Start with **Cutline**: edit, preview and render
local videos with Codex, Claude Code and other compatible AI tools.

## Install

```sh
npx skills add itering/hast-kit -g
```

Choose the AI tools you use when prompted. To select Cutline explicitly:

```sh
npx skills add itering/hast-kit --skill cutline -g
```

For an unattended installation into Codex and Claude Code:

```sh
npx skills add itering/hast-kit --skill cutline -g -a codex -a claude-code -y
```

Reload your AI tool if the skill is not detected. Open the folder containing your
footage and ask:

> Use the Cutline skill to set up the local editing environment, trim my video
> to 30 seconds, add a title, preview it, and export an MP4 to output/.

中文示例：

> 使用 Cutline Skill，先检查并配置本地剪辑环境，把素材剪成 30 秒，加上标题，
> 先生成预览，再导出 MP4 到 output 文件夹，保留原始素材。

## What gets installed

The command installs Skill instructions. On first use, your AI tool follows the
[setup guide](skills/cutline/references/install.md) to install the published
[Cutline CLI](https://www.npmjs.com/package/@hast/gtm-cutline) and check rendering
dependencies. Setup runs subject to your AI tool's normal execution permissions.

Cutline 0.1.0 requires Node.js 24+, FFmpeg/ffprobe, libass for FFmpeg text rendering,
and matching Playwright Chromium. You can also install the CLI yourself:

```sh
npm install -g @hast/gtm-cutline@0.1.0
cutline doctor
```

Editing, project files, previews and video rendering stay on your computer.
Optional HAST component generation and narration require a separate HAST API key
with models:invoke permission and a compatible deployment endpoint. Only the
content needed for those inference calls is sent to HAST. Never paste API keys
into a chat. See the [generation guide](skills/cutline/references/hast.md).

## Included skills

| Skill | Purpose |
| --- | --- |
| [cutline](skills/cutline/SKILL.md) | Local video editing, HTML components, narration, preview and MP4 export |

This repository distributes Skill instructions and references. The CLI is
distributed separately through npm; HAST's service implementation is not included.

To refresh the installed Skill, rerun the installation command above.
