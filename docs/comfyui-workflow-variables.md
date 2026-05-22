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
| `%reference_image%` | Uploaded ComfyUI image filename for the current ComfyUI reference image, or an empty string when no reference image is set. Card-image and uploaded character references resolve through this same placeholder. |

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

1. Add a `LoadImage` node in ComfyUI.
2. Export the workflow in API format.
3. Set that node's `image` input to `%reference_image%`.

If no character reference image is selected, `%reference_image%` becomes an empty string.

The built-in Auto mode uses the Flux.2 Klein workflow when `%reference_image%` exists. That graph loads the image, encodes it through the Flux.2 VAE, and passes it into `ReferenceLatent`. Use `Use Card Image` for the current character card image or upload a clearer reference in the Character Identity section. Card-image capture saves immediately; uploaded references persist with `Save Char`.

When Auto has no character reference image, it switches to the built-in Z-Image Turbo text-generation graph instead of sending an empty reference into Flux.2 Klein.

## Notes

- Placeholders are replaced only in JSON string values.
- Use API-format JSON, not the regular visual workflow export.
- If the custom workflow JSON cannot be parsed, imguwu falls back to its built-in default workflow.
- Runtime ComfyUI or proxy JSON errors are reported as runtime errors instead of being relabeled as invalid workflow JSON.
