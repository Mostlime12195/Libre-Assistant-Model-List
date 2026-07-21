# Libre Assistant Model Configuration

Remote model list for [Libre Assistant](https://github.com/Mostlime12195/Libre-Assistant).  
Editing this file updates the model picker and API behavior.

## Updating models

If you wish to request a change to the model list, follow these steps:

1. Fork this repo
2. Edit model-list.json with the requested changes (make sure you read the schema!)
3. Create a PR (pull request) and describe your changes

A maintainer (likely me!) will review your PR soon and choose whether to merge it or not! If merged, the model list automatically updates and all instances of Libre-Assistant along with it.

### NOTES
- If you are adding a model from a provider that does not yet exist in the model list, please add the provider yourself and place a black-and-white svg logo of the provider (from lobehub.com) into /logos/.
- Providers should always be shown in alphabetical order
- Models will be listed mostly by version (newer versions at the top, older versions at the bottom) and in order of strength/power/scale when there are more than one model of the same version (i.e. Claude Opus 4 would be above Claude Sonnet 4, but Claude Sonnet 4.5 would be above Claude Opus 4).

## Schema

### Top-level

| Field | Type | Description |
|-------|------|-------------|
| `version` | `number` | Schema version. |
| `defaultModelId` | `string` | The default model. |
| `categories` | `array` | Grouped model lists (e.g., by provider). |

### Category object

| Field | Type | Description |
|-------|------|-------------|
| `category` | `string` | Display name of the group. |
| `logo` | `string` | Path to the logo asset. |
| `models` | `array` | List of models in this category. |

### Model object

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `id` | `string` | — | OpenRouter model ID (e.g., `moonshotai/kimi-k2.6`). |
| `name` | `string` | — | Human-readable name in the UI. |
| `description` | `string` | — | Subtitle shown in the model picker. |
| `vision` | `boolean` | `false` | Supports image/vision input. |
| `tool_use` | `boolean` | `true` | Supports tool/function calling. **Omit or set `true` to enable.** |
| `providers` | `string[]` | — | Restrict to specific OpenRouter provider(s). |
| `reasoning` | `object` | — | Reasoning behavior (see below). |

### Reasoning configuration

| Field | Type | Description |
|-------|------|-------------|
| `supported` | `boolean` | **Required.** Does the model support reasoning at all? |
| `toggleable` | `boolean` | Can the user turn reasoning on/off? |
| `defaultEnabled` | `boolean` | If `toggleable`, initial on/off state. |
| `effort` | `object` | `{ levels: string[], default: string }` — effort level options. |
| `alternateModel` | `string` | If set, switch to this model ID when reasoning is enabled. |

### API parameter mapping

| Model config | User setting | API output |
|--------------|--------------|------------|
| `supported: false` | — | `{}` (nothing) |
| Always-on, no effort | — | `{}` (nothing) |
| Always-on + effort | effort | `{ effort: "..." }` |
| Toggleable | enabled | `{ enabled: true }` |
| Toggleable | disabled | `{ enabled: false }` |
| `alternateModel` | enabled | `model: "alt-model-id"` |
| `alternateModel` | disabled | `{}` (original model) |

## Legacy compatibility

The main app still normalizes old shorthand formats (`reasoning: true`, `reasoning: false`, `reasoning: [true, false]`, `reasoning: "model-id"`) to the current schema. Prefer the explicit object format for new entries.

## Validation

Before committing, you can validate the JSON:

```bash
# Basic syntax check
node -e "JSON.parse(require('fs').readFileSync('models.json'))"

# Or with jq
jq empty models.json
```
