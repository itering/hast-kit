# Hast asset generation

Use `cutline hast login` to start browser authorization. The user signs in to
HAST Account, chooses the Workspace, checks the terminal code, and explicitly
approves HAST GTM CLI's `models:invoke` permission. Wait for the user to complete
this step; do not approve on their behalf or ask for keys in chat.

Use `--base-url <Backplane-origin/api/public/v1>` for a private or DEV deployment,
or set `HAST_API_BASE_URL`. The deployment must enable the device authorization
endpoints and configure its trusted Account UI origin. Existing saved settings
are reused; otherwise the CLI uses the public HAST endpoint.

On SSH/headless hosts use `--no-browser` and open the printed URL on another
computer. macOS uses Keychain, Linux uses Secret Service (`secret-tool`), and
Windows stores CurrentUser DPAPI-encrypted credentials. If no keyring is available
on Linux/macOS, explicitly select `--credential-store file` for an unencrypted
owner-private file outside the video project. Do not silently choose this fallback.

Short-lived access tokens refresh automatically. Account > Login & security
lists authorized CLI devices and can revoke them immediately. `cutline hast
logout` revokes browser authorization before deleting local credentials; if the
server is unreachable, retry logout later or revoke in Account settings.

For automation, `cutline hast login --base-url <URL> --from <private-key-file>`
retains API-key login. The key must grant `models:invoke`; importing it does not
verify server authorization. Do not substitute a cloud Agent task or provider key.
`hast status` describes local configuration; use `cutline hast models` to verify
access and read available Hast text models/prices, and verify `anthropic/claude-opus-5.5` is available. Do not substitute another model without the user's instruction.

## Creative planning

For creative direction and animation design, call HAST's Claude Opus 5.5 through
Cutline. The host AI gathers the brief and local material summaries, orchestrates
the calls, checks the output and edits locally; it must not silently replace
this creative stage with its own model. Claude Code is a client, not the model.

```sh
cutline hast plan --prompt-file brief.txt --model anthropic/claude-opus-5.5 --out creative-plan
```

This returns `plan.md`, the raw `response.json` and `usage.json`. Review the plan
against the brief, resolve unsupported claims and missing inputs, then supply it
to component generation. User approval is needed only when their workflow calls
for it. An existing user-approved plan can be reused instead of regenerated.
The command sends text, not the original video. Extract relevant descriptions or
transcripts locally; image/keyframe and video uploads are not supported here.

## Components

Write the user's brief to a local text file, then call:

```sh
cutline hast component --prompt-file brief.txt --plan-file creative-plan/plan.md --model anthropic/claude-opus-5.5 --seconds 8 --width 1920 --height 1080 --fps 30 --out generated-scene
```

Both creative commands default to Opus 5.5 when no explicit or saved model is set.
Use the explicit model flag above to avoid a legacy saved model overriding this
workflow. Only change it when the user requests another model; never fall back
on failure. Each command is a separate billable call.

The output includes a checked native `main.cutline.json` and a `run.cutline.json`.
Preview with `cutline studio --run run.cutline.json --workspace generated-scene`.
To combine with another film, inspect and merge the Component node and its
referenced timing into that film, avoiding ID collisions. Preserve the local
seconds-based time contract. Run strict checks and visually inspect the
combined result.

The model-facing intermediate format is
`{"format":"cutline-program@1","html":"body fragment","css":"styles","render":"function body"}`.
Cutline deterministically wraps it into its native author structure. Do not
interpret arbitrary returned HTML as an author document. Animation must be
deterministic under absolute time and backward seeking; no external network
assets are required by this contract. Use unique component-prefixed CSS class names and matching render selectors to
avoid collisions with the renderer page (for example, avoid bare `.header` or
`.title`). Inspect generated layouts and repair locally before export.
A structural check does not guarantee
visual quality or correct generated JavaScript.
The Program argument named `localFrame` is elapsed seconds in the current
Cutline runtime; use it directly as time.

## Narration

```sh
cutline hast speech --text-file narration.txt --voice Aoede --out generated-voice
```

Optionally pass `--instructions-file direction.txt`. The model is
`google/gemini-3.8-flash-tts`. Read `audio.json` for the actual duration and use
`narration.wav` as local audio. Adjust the timeline to the generated duration;
do not infer word alignment from a total duration or claim synthesized speech
includes aligned captions.

## Billing and recovery

Generation consumes Hast usage. Use an existing intact output directory to
reuse an identical result. Changed or incomplete directories are rejected.
Do not automatically retry unknown outcomes into a new directory: first inspect
usage and obtain direction when a repeat would create an unexpected charge.
Use saved `response.json` to repair component output locally when possible.
The returned chat token usage is not a currency quote; speech cost is available
through Hast account billing, not the WAV file.
