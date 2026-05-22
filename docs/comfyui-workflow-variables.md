# ComfyUI Workflow Variables

Custom ComfyUI workflows use API-format JSON from ComfyUI's `Save (API Format)` export. Paste that JSON into `Custom Workflow JSON`.

imguwu replaces placeholders anywhere inside string values before sending the workflow to `/prompt`.

| Placeholder | Value |
| --- | --- |
| `%prompt%` | Final positive prompt after LLM prompt generation, style, quality tags, ST Style, contextual filters, and wildcard expansion. |
| `%negative%` | Final negative prompt after ST Style, contextual filters, and wildcard expansion. |
| `%seed%` | Resolved seed for this image. If the seed is `-1`, QIG resolves a random seed before sending the workflow. |
| `%width%` | Current width setting. |
| `%height%` | Current height setting. |
| `%steps%` | Current steps setting. |
| `%cfg%` | Current CFG scale setting. |
| `%denoise%` | Current ComfyUI denoise setting. |
| `%clip_skip%` | Current ComfyUI clip skip setting. |
| `%sampler%` | ComfyUI sampler name derived from the selected sampler. |
| `%scheduler%` | Current ComfyUI scheduler setting. |
| `%model%` | Current Local Model field. |
| `%reference_image_base64%` | JSON array containing the current ComfyUI reference image as a base64 data URL, or an empty string when no reference image is set. Card-image and uploaded character references resolve through this same placeholder. |
| `%reference_image%` | Legacy empty filename placeholder. Use `%reference_image_base64%` for new reference workflows. |

## Typed Values

When a node input is exactly a placeholder, QIG preserves numeric types where possible:

```json
"seed": "%seed%",
"width": "%width%",
"cfg": "%cfg%"
```

Those become numbers in the submitted workflow. If a placeholder is embedded inside other text, it is replaced as text:

```json
"filename_prefix": "qig_%seed%"
```

## Reference Images

To use the character reference image in a custom workflow:

1. Add a base64-aware reference node such as
   `ReferenceChainConditioningBase64` from `ComfyUI-ReferenceChain`.
2. Export the workflow in API format.
3. Set that node's `images_base64` input to `%reference_image_base64%`.

If no character reference image is selected, `%reference_image_base64%`
becomes an empty string.

imguwu fetches the saved reference from SillyTavern at generation time and
sends it inside the ComfyUI `/prompt` request. That keeps reference workflows
off both static image paths on the ComfyUI machine and the ComfyUI multipart
upload endpoint.

The built-in Auto mode uses the Flux.2 Klein workflow when a saved reference
exists. That graph passes `%reference_image_base64%` to
`ReferenceChainConditioningBase64`, which decodes the transport-safe reference
before the built-in graph feeds it through the Flux.2 image-edit VAE and core
`ReferenceLatent` branch. Use `Use Card Image` for the current character card
image or upload a clearer reference in the Character Identity section.
Card-image capture saves immediately; uploaded references persist with
`Save Char`.

After ComfyUI finishes, imguwu fetches the generated image back from ComfyUI
before SillyTavern displays, inserts, or saves it. Chat output therefore does
not rely on a ComfyUI `/view` URL remaining reachable.

When Auto has no character reference image, it switches to the built-in Z-Image Turbo text-generation graph instead of sending an empty reference into Flux.2 Klein.

## Notes

- Placeholders are replaced only in JSON string values.
- Use API-format JSON, not the regular visual workflow export.
- If the custom workflow JSON cannot be parsed, imguwu falls back to its built-in default workflow.
- Runtime ComfyUI or proxy JSON errors are reported as runtime errors instead of being relabeled as invalid workflow JSON.
