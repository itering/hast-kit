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
cutline build runs/preview.cutline.json --workspace my-edit --profile final
```

Run file paths are relative to the selected workspace; inspect `cutline paths
--workspace my-edit` when resolving ambiguity.

Final rendering requires FFmpeg and ffprobe. Caption/text rendering through the
default FFmpeg engine requires a build with libass. Raster rendering uses
Playwright Chromium. Keep renderer prerequisites separate from model-provider
credentials: local editing does not inherently require a cloud account.

Use `cutline vocabulary component` to inspect Present, Program and other visual
elements. A Program's render function receives `(root, localFrame, data)`.
Despite the legacy parameter name, `localFrame` is elapsed time in seconds
relative to the Present start. Use it directly for time-based animation; do not
divide it by the composition frame rate.
