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

## Scope each component request

Cutline 0.4.1 allows at most 32,000 output tokens for `hast component`; `hast plan`
remains at 8,000. This is a maximum allowance, not an instruction to generate
32,000 tokens. Ask for concise, complete output. Reasoning may consume the budget
before final code is returned; a larger ceiling does not make whole-video
component generation reliable. Older CLI versions may have a lower ceiling;
follow the [installation guide](install.md) to use this release.

Plan the film as a whole, then generate one coherent scene or focused reusable
component per call. Split by independent layouts, animated elements and expected
code complexity, not a fixed number of seconds. A short dense scene can need more
splitting than a long simple one. Keep the script, captions, footage placement
and final timeline assembly local when they do not belong inside the component.

Keep a shared style guide for canvas size, fonts, colors, spacing and motion.
For each scene, supply that guide, the relevant plan excerpt, exact copy, layout,
local timing and end state. Save the excerpt as a scene-specific plan file for
`--plan-file`; do not repeat the entire script and every scene's implementation
requirements in each request. Preserve the original full plan for assembly.
Use distinct scene IDs and output directories. Check and preview each result,
reuse completed components, then assemble and inspect their boundaries locally.

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

### Recover by failure type

Preserve the original response, receipt and any successful scene artifacts. Do
not delete a failed receipt or edit its status to bypass duplicate-call checks.
For local repairs, copy available generated files into a separate editable
project; an edited generated directory is no longer an intact cache entry.

- **Budget exhausted, empty content:** inspect `response.json` and its usage.
  There is no component code to continue. Split or simplify the scene before
  making a new call; do not resend the same oversized request unchanged.
- **Budget exhausted, partial content:** retain it as a draft. Reuse complete
  portions only after checking them; do not assume truncated JSON or JavaScript
  is a valid component. Prefer a smaller replacement for the missing portion.
- **Complete response, invalid JSON or compilation:** first repair syntax,
  references or author structure locally using the saved response and any author
  files. Run strict checks and preview the repaired component. If native files
  were never created, inspect the installed vocabulary and component contract
  before constructing them; do not pass the intermediate Program JSON as an author.
- **Checks pass, visual result fails:** identify the specific scene and defect,
  repair that component locally, and preview it and its boundaries. Keep the
  successful scenes instead of regenerating the whole film.
- **Timeout or connection failure:** billing outcome may be unknown. Inspect the
  local receipt, any saved response and available account usage first. If the
  outcome cannot be established, report it and obtain direction before another
  potentially duplicate charge. Never silently switch providers or models.

A revised, deliberately scoped generation uses a new output directory and is a
new billable request, not a free continuation. Stay within the user's authorized
scope and budget. If the same failure repeats after a targeted repair, simplify
or split further rather than looping unchanged requests; retain completed work
and report what remains unresolved. Verify the combined film after recovery.
