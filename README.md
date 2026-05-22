# imguwu - SillyTavern Character Images

`imguwu` is a ComfyUI-first SillyTavern extension for generating the current
character in the current scene without maintaining a LoRA for every card.

It is a focused fork of Quick Image Gen. The UI exposes ComfyUI only and ships
with built-in workflows for two cases:

- Flux.2 Klein reference generation when a character reference image exists.
- Z-Image Turbo text generation when no reference image exists.

## Install

Install this extension from SillyTavern's extension installer with:

```text
https://github.com/aniachan/imguwu
```

ComfyUI must be reachable from the browser. If it runs on another origin,
start ComfyUI with CORS enabled for your SillyTavern origin.

## Character Setup

Open a character chat and expand the `imguwu` panel.

1. In `Character Identity`, click `Use Card Image` to copy the current card
   image into the character reference slot, or `Upload Reference` to use a
   cleaner image.
2. Click `Extract From Card` to ask the active SillyTavern text model for a
   concise appearance-only `Visual Identity`, or write that field manually.
3. Click `Save Char` after manual identity or upload edits.

The saved `Visual Identity` is kept separately from the raw character card.
Prompt generation uses it before noisy card description text. The saved
reference image is character-scoped and feeds ComfyUI `%reference_image%`.

For best identity retention, use a clear reference where the face, hair, eyes,
signature accessories, and body traits are visible. A card image is the easy
default; an uploaded reference is the quality override.

## Default Generation Flow

Keep `Built-in Workflow` set to `Auto` for the intended flow:

- A saved reference image selects the Flux.2 Klein reference workflow.
- No reference image selects the Z-Image Turbo workflow.

Use the prompt controls with chat context and LLM prompt generation enabled to
turn the active roleplay situation into a natural image prompt. The generated
prompt receives the character visual identity and the relevant chat scene
before it reaches ComfyUI.

## ComfyUI Defaults

The built-in Flux workflow expects:

- An edit/reference Flux.2 Klein UNET in the `localModel` field.
- `qwen_3_8b_fp8mixed.safetensors` as the Flux.2 text encoder.
- `flux2-vae.safetensors` as the Flux.2 VAE.
- ComfyUI nodes that include `FluxKVCache`, `ReferenceLatent`,
  `EmptyFlux2LatentImage`, and `Flux2Scheduler`.

The built-in Z-Image workflow expects:

- `z-image-turbo\moodyProMix_zitV12DPO.safetensors` by default.
- `Qwen3-4b-Z-Image-Turbo-AbliteratedV1.safetensors` as the text encoder.
- `ae.safetensors` as the VAE.

Change the model fields in the ComfyUI settings when your local filenames or
folders differ.

## Custom Workflows

Built-in workflows are the clean default. Custom API-format ComfyUI workflow
JSON still works for experiments and model swaps.

The workflow placeholder reference is in
[`docs/comfyui-workflow-variables.md`](docs/comfyui-workflow-variables.md).
The important character placeholder is `%reference_image%`; imguwu uploads the
current saved reference to ComfyUI before replacing it.

## Included Workflow Features

- Character-scoped visual identity text and ComfyUI reference image storage.
- Card-image reference capture and reference uploads.
- LLM-assisted scene prompt generation from SillyTavern chat context.
- Prompt history, galleries, presets, contextual filters, and image insertion.
- ComfyUI workflow presets and custom workflow JSON placeholders.

## License

MIT
