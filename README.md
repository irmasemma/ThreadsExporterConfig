# ThreadsExporterConfig

Public runtime config for the **Threads Downloader & Exporter** Chrome extension.

The extension fetches these JSON files at runtime from `raw.githubusercontent.com`, so editing a
file here changes the live extension **without shipping a new version or a store review**. Changes
propagate within ~30 minutes (the extension caches with a TTL).

> This repo must stay **public** — `raw.githubusercontent.com` only serves public repos.

## Files

| File | Purpose | Raw URL |
|------|---------|---------|
| `cta-config.json` | The "export on the web" CTA card shown in the export overlays (copy, link, visibility). | `https://raw.githubusercontent.com/irmasemma/ThreadsExporterConfig/main/cta-config.json` |
| `ad-config.json` | Ad link config. | `https://raw.githubusercontent.com/irmasemma/ThreadsExporterConfig/main/ad-config.json` |

---

## `cta-config.json`

```jsonc
{
  "enabled": true,                 // master on/off for the whole CTA
  "free": {                        // shown to free users
    "enabled": true,               // per-tier on/off
    "line": "benefit text…",       // sentence above the button
    "buttonText": "Label →",       // include your own trailing arrow if you want one
    "url": "https://…"             // MUST start with https:// (anything else is ignored)
  },
  "pro": {                         // shown to Pro / Pro+ users
    "enabled": true,
    "line": "…",
    "buttonText": "…",
    "url": "https://…"
  }
}
```

### Common edits

- **Hide the CTA everywhere:** set `"enabled": false`.
- **Hide it for paid users only:** `"pro": { "enabled": false }`.
- **Change wording / link:** edit `line`, `buttonText`, `url`.

### Safety / robustness

The extension is defensive about this file, so a mistake here can't break exports:

- Any missing field falls back to a built-in default baked into the extension.
- If the fetch fails (offline, 404, invalid JSON), the last cached config — or the built-in
  defaults — is used.
- `url` is accepted only if it starts with `https://` (blocks `javascript:` and other schemes).
- Text is rendered as plain text (`textContent`), so it can never inject HTML or scripts.

### Validate before you commit

Make sure the file is valid JSON (a trailing comma will make the fetch silently fall back to
defaults). Any JSON linter works, e.g.:

```bash
python -m json.tool cta-config.json
```

## Consumed by

`ThreadsExporterFinal` → `src/ExportUserThreads.js` (constant `CTA_CONFIG_URL`). See that repo's
`docs/cta-remote-config.md` for the extension-side details.
