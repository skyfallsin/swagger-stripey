# swagger-stripey Agent Guide

Single-file Stripe-style API docs renderer for OpenAPI 3.x specs.

## Files

- `index.html` — The entire docs engine. One file, zero dependencies.
- `example/` — Demo with Swagger Petstore spec

## How Customization Works

All configuration lives in a `<script id="docs-config" type="application/json">` block inside `<head>`:

```html
<script id="docs-config" type="application/json">
{
  "specUrl": "./openapi.json",
  "logoUrl": "./logo.png",
  "title": "My API",
  "subtitle": "API Reference",
  "defaultTheme": "dark",
  "accent": "#9B30FF",
  "accentLight": "#B366FF",
  "methodGet": "#9B30FF"
}
</script>
```

Alternatively, set `window.docsConfig` before the main script runs.

### Adding a Config Option

1. Add the key to `conf` in the JS section of `index.html`
2. If it maps to a CSS variable, add it to `conf.theme` and define the variable in both `:root` (dark) and `[data-theme="light"]`
3. If it affects rendering logic, use it in the `render()` function
4. Document it in the Config Reference table in `README.md`

### Theming

Two sets of CSS custom properties:

- **Dark** — defined on `:root, [data-theme="dark"]`
- **Light** — defined on `[data-theme="light"]`

`conf.theme` overrides apply to the dark theme only. Light theme has its own adjusted palette that maps the same accent into legible-on-white variants. If you need different light theme colors, edit the `[data-theme="light"]` block directly.

### Responsive Breakpoints

- **Desktop (>1024px)** — three-panel: sidebar + description + code
- **Tablet (768-1024px)** — sidebar + stacked description/code (code panel below)
- **Mobile (<768px)** — collapsible sidebar (hamburger menu), single column

### OpenAPI Spec Support

The renderer handles:
- `paths` with all HTTP methods (get, post, put, patch, delete)
- `parameters` (path, query) with types, enums, required flags
- `requestBody` with `application/json` schemas (renders property tree)
- `responses` with status codes and descriptions
- `tags` for grouping and ordering (uses spec `tags` array order)
- `deprecated` flag on operations
- `security` requirements
- `servers` for base URL display and curl examples
- `info` for title, description, version
- `$ref` to `components/schemas` (resolves one level for property display)

It does **not** handle:
- Deep `$ref` resolution (nested `$ref` chains)
- `allOf` / `oneOf` / `anyOf` composition
- XML request/response bodies
- OAuth flow rendering
- Webhooks / callbacks
- Non-OpenAPI formats (GraphQL, Protobuf, RAML, API Blueprint, etc.) — convert to OpenAPI first, then point swagger-stripey at the JSON. Conversion tools exist for all major formats.

### Code Style

- Plain vanilla JS — no framework, no build step, no modules
- CSS custom properties for all colors/spacing
- Template literals for HTML generation
- All rendering in a single `render()` async function
- `esc()` for HTML escaping all user/spec content

### Testing

Open `example/index.html` in a browser with a local server:

```bash
cd example && python3 -m http.server 8080
```

Verify:
- All operations from the spec appear in the sidebar
- Dark/light toggle works and persists across refresh
- Mobile: hamburger menu opens/closes sidebar
- Tablet: code panel stacks below description
- Curl examples match the spec's paths and request bodies
