# Local project workflow

The initializer supplies these files:

- `authors/main.cutline.json`: editable script, media, tracks and film.
- `recipes/look.cutline.json`: reusable appearance.
- `runs/preview.cutline.json`: author entry and output targets.
- `cutline.runtime.json`: provider configuration with credential references.

The starter references placeholder footage. Point it at the user's local media
before expecting a successful media build.

```sh
cutline init my-edit
cutline vocabulary
cutline check authors/main.cutline.json --workspace my-edit --strict
cutline plan runs/preview.cutline.json --workspace my-edit --profile preview
cutline studio --run runs/preview.cutline.json --workspace my-edit
cutline build runs/preview.cutline.json --workspace my-edit --profile final --engine raster
```

Run file paths are relative to the selected workspace; inspect `cutline paths
--workspace my-edit` when resolving ambiguity.

## Choose an export engine

Recommend `--engine raster` unless the user's priorities or project justify
FFmpeg. Always pass the engine explicitly; omitting it currently selects ffmpeg.

| Consideration | `raster` (recommended) | `ffmpeg` |
| --- | --- | --- |
| How it works | Captures the browser Stage frame by frame, then encodes video and muxes audio with FFmpeg. | Composites supported elements through FFmpeg filters; HTML Programs still use browser-rendered overlays. |
| Visual fidelity | Reproduces Stage layout, HTML/CSS, animated position, rotation and blend modes. | Good for supported cuts, boxes, media and text; some Stage effects cannot be reproduced. |
| Performance | Usually slower and more resource intensive, especially for long or high-resolution videos. | Usually faster for simple compositions; Program overlays still incur browser work. |
| Dependencies | FFmpeg, ffprobe and matching Playwright Chromium. Browser-rendered captions do not require libass. | FFmpeg and ffprobe; text/captions require libass, and Program overlays require Chromium. |
| Choose when | Visual fidelity matters, the project uses animation/components, or engine compatibility is uncertain. | The user prioritizes speed and the project uses supported simple visuals; verify a representative preview/export. |

For a normal export, proceed with the selected engine without a mandatory
`cutline doctor` check. If export fails, diagnose the reported dependency for
that engine. The optional doctor command checks all capabilities, so a failing
libass/captions check alone is not a reason to block raster or install another
FFmpeg. Raster still requires FFmpeg for encoding and audio.

```sh
# Recommended: frame-by-frame capture
cutline build runs/preview.cutline.json --workspace my-edit --profile final --engine raster
# Faster alternative for compatible compositions
cutline build runs/preview.cutline.json --workspace my-edit --profile final --engine ffmpeg
```

Keep renderer prerequisites separate from model-provider credentials: local
editing does not inherently require a cloud account.

## Component timing

Use `cutline vocabulary component` to inspect Present, Program and other visual
elements. A Program's render function receives `(root, localFrame, data)`.
Despite the legacy parameter name, `localFrame` is elapsed time in seconds
relative to the Present start. Use it directly for time-based animation; do not
divide it by the composition frame rate.
