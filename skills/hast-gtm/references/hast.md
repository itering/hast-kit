# Hast asset generation

Use `cutline hast --help` or `cutline --help` for the installed syntax. A
deployment must expose the public model endpoints and the user's Workspace API
key must grant `models:invoke`. Do not substitute a cloud Agent task or an
OpenRouter key.

The user obtains the API key in Hast's AIP API-key settings. Import from a private
file with `cutline hast login --base-url <Backplane-origin/api/public/v1> --from
<key-file> --model <available-Hast-text-model>`. Never ask for keys in chat.
`hast status` reports local configuration, not proof of server authorization.
Use `cutline hast models` to read the active Hast text models and listed token
prices, then choose the model ID for component generation.

## Components

Write the user's brief to a local text file, then call:

```sh
cutline hast component --prompt-file brief.txt --seconds 8 --width 1920 --height 1080 --fps 30 --out generated-scene
```

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
assets are required by this contract. A structural check does not guarantee
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
