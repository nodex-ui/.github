# Nodex UI

**Nodex UI** is an HTML-first, framework-independent UI component library for the web. It ships its own styling and behavior, loads straight from a CDN or npm, and needs no build step. Use it as **Nodex components** (`<Button/>`-style imports in your own application) or as the static `b-ui` markup adapter for plain HTML, server-rendered pages and CMS pages.

- npm package: [`nodexui`](https://www.npmjs.com/package/nodexui) · current version **1.3.1**
- Website, live demos and docs: <https://ui.thebase.vn>
- Release history: [CHANGELOG.md](CHANGELOG.md)

## Why Nodex UI

- **No build step.** Every component runs from a CDN `<script>` or import map; nothing to compile.
- **No framework lock-in.** Vanilla JavaScript/TypeScript, no JSX, no React or Vue required.
- **Modular.** Each component is a self-contained unit you import or mark up on its own (75 in the current registry).
- **Dynamic-friendly.** Components mount, unmount and re-render from user-driven markup, which suits WYSIWYG website builders and drag-and-drop editors.
- **Batteries included.** Lucide icons, light/dark theming, PWA tooling, ApiDoc, i18n and cookieless analytics (the last three are optional standalone bundles).

## Install

### npm

```sh
npm install nodexui
```

```js
import { Component, mount, xml } from "nodexui";
import { Button, Card, CardBody, CardHeader, CardTitle } from "nodexui";
import "nodexui/nodexui.css";
```

Subpath exports: `nodexui/analytics`, `nodexui/i18n`, `nodexui/qr`, `nodexui/apidoc`, `nodexui/icons`, `nodexui/pwa`, `nodexui/vite`, `nodexui/registry`.

### CDN (Nodex components)

Both APIs load from jsDelivr or unpkg. The snippets use `@latest` for readability; pin an exact version (`@1.3.1`, or `@1` for non-breaking updates) on any production page.

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/nodexui@latest/dist/nodexui.min.css">
<script type="importmap">
{
  "imports": {
    "nodexui": "https://cdn.jsdelivr.net/npm/nodexui@latest/dist/nodexui.esm.js",
    "nodexui/templates": "https://cdn.jsdelivr.net/npm/nodexui@latest/dist/nodexui.templates.xml"
  }
}
</script>
<script type="module">
  import { Component, mount, xml } from "nodexui";
  import { Button, Card, CardBody, CardHeader, CardTitle } from "nodexui";

  const templates = await fetch(import.meta.resolve("nodexui/templates")).then((r) => r.text());

  class Root extends Component {
    static components = { Button, Card, CardBody, CardHeader, CardTitle };
    static template = xml`
      <Card>
        <CardHeader><CardTitle>Hello</CardTitle></CardHeader>
        <CardBody>
          <Button variant="'default'" label="'Save'" onClick="() => console.log('saved')"/>
        </CardBody>
      </Card>`;
  }

  await mount(Root, document.getElementById("app"), { templates });
</script>
```

Swap `cdn.jsdelivr.net/npm` for `unpkg.com` if jsDelivr is unreachable. See [Nodex Components](docs/owl-components.md) for the full API and bundler usage.

### CDN (static `b-ui` markup)

With no application of your own, use one stylesheet and one script tag, no import map:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/nodexui@latest/dist/nodexui.min.css">
<script src="https://cdn.jsdelivr.net/npm/nodexui@latest/dist/nodexui.min.js" defer></script>
```

`dist/nodexui.min.js` and `dist/nodexui.esm.js` are self-contained and back both APIs: the JS bundle, the Nodex runtime, every component class and the theme helper are embedded in load order. There is no separate runtime, components or theme file.

Every component ships both APIs from `src/components/<name>/`: the static adapter (`<name>.js`, using `b-att-*` attributes and `nodexui:*` events) and a component class (`<name>.component.js`). A few components are intentionally reshaped in the component API, notably `chart` and the data-driven `data-table`, `combobox`, `command` and `calendar`. Each page under `docs/components/` explains the specifics.

## Theming

Colour tokens, `--base-radius` and `--b-overlay` live in the downloadable `nodexui-light.css` and `nodexui-dark.css` themes. The **Theme** tab of the examples app (`examples/index.html#/theme`) builds a custom pair for you. For app-level light/dark toggles, import `createThemeController` / `NodexUITheme` as `nodexui` (import map) or from `nodexui` (bundler). See [Theming](docs/theming.md).

## Optional services

| Service | Import | What it is |
| --- | --- | --- |
| **Analytics** | `nodexui/analytics`, `dist/nodexui.analytics.min.js` | Cookieless, standalone (about 5.6 KB min). Nothing is collected until `init()` is called. Covers pageviews (including SPA), engagement, outbound links, downloads and forms. See [Analytics](docs/analytics.md) and [`examples/analytics/`](examples/analytics/). |
| **i18n** | `nodexui/i18n`, `dist/nodexui.i18n.min.js` | Gettext-style `.po` catalogs, no runtime dependencies. See [i18n](docs/i18n.md) and [`examples/i18n/`](examples/i18n/). |
| **QR / VietQR** | `nodexui/qr`, `dist/nodexui.qr.min.js` | QR code SVG/matrix generator plus the VietQR payment payload builder, standalone. See [QR](docs/qr.md) and [`examples/qr/`](examples/qr/). |
| **ApiDoc** | `nodexui/apidoc` | Renders an OpenAPI 3 JSON file as browsable docs with a Try It panel and 17 code-sample languages. See [ApiDoc](docs/components/apidoc.md) and [`examples/apidoc/`](examples/apidoc/). |
| **PWA toolkit** | `nodexui/pwa`, `nodexui/vite` | `buildManifest`, `buildServiceWorker` and the `nodexuiPwa()` Vite plugin. |

```html
<script type="module">
  import { init, track } from "https://cdn.jsdelivr.net/npm/nodexui@1.3.1/dist/nodexui.analytics.esm.js";
  init({ backendUrl: "https://stats.example.com" }); // events POST to <backendUrl>/api/event
  track("Signup", { props: { plan: "pro" } });
</script>
```

```js
import { init, t, loadCatalog } from "https://cdn.jsdelivr.net/npm/nodexui@1.3.1/dist/nodexui.i18n.esm.js";
init({ languages: [{ code: "en", label: "English" }, { code: "vi", label: "Tiếng Việt" }] });
await loadCatalog(import.meta.url); // fetches ./i18n/vi.po when Vietnamese is picked
console.log(t("Hello {name}", { name: "An" }));
```

### Project CLI

`create-nodexui-app` scaffolds a static-importmap or Vite PWA project (`create`, `init`, `convert`) and generates the manifest, service worker and icons (`pwa`):

```sh
npx create-nodexui-app create my-app
npx create-nodexui-app pwa . --config nodexui.pwa.config.js
```

See [Usage Guide §5b](docs/usage-guide.md) and the [PWA component](docs/components/pwa.md).

## Search and discoverability

The public site <https://ui.thebase.vn> keeps its hostname and URLs under the Nodex UI brand. The examples app sets per-route title, description, canonical and Open Graph tags (`examples/seo.js`), ships `sitemap.xml`, and reports first-party events through `examples/tracking.js`. Search Console is used for search visibility; Google Analytics is not. The plan, constraints and KPIs are in [the SEO, developer marketing and analytics brief](.agents/knowledge/base-ui-seo-analytics-ai-agent-brief.md).

## Versioning policy

Nodex UI follows [Semantic Versioning](https://semver.org/) from **1.3.0**. The public API covered by this promise is:

- Component classes, their names, props, slots and callback props exported from `nodexui`.
- Static markup: `b-ui` / `b-icon` names, `b-att-*` attributes and `nodexui:*` events.
- CSS: `b-*` class names and `--b-*` design tokens.
- Runtime exports (`NodexUI`, `Component`, `mount`, `xml`, `cn`, `mergeStyle`, theme helpers), the published `dist/` file names and the package `exports` paths.

**Major** releases remove or rename any of the above (listed under **Breaking** in the CHANGELOG with a migration note), **minor** releases add components, props, variants, tokens or icons, and **patch** releases fix bugs without changing the API. Names prefixed `_`, undocumented internals, `docs/` and `examples/` sources, and the authoring helpers in [Component Authoring](docs/component-authoring.md) are not public API. Deprecated APIs keep working until the next major and are flagged in the CHANGELOG first.

Pin an exact version (`@1.3.1`) for byte-identical output, or the major range (`@1` on jsDelivr/unpkg, `^1.3.1` in npm) to receive non-breaking updates. Avoid `@latest` in production.

## Documentation

- [Usage Guide](docs/usage-guide.md): the entry point for integrating Nodex UI into a project.
- [Installation](docs/installation.md): CDN, npm, self-hosting, icon assets, pinned versions, release guardrails.
- [Nodex Components](docs/owl-components.md): the component API, what to load, common mistakes.
- [Runtime API](docs/runtime-api.md): the `window.thebase` global, mounting lifecycle, theming API, destroy behavior.
- [Registry](docs/registry.md): `dist/nodexui.registry.json`, build files, component and block metadata.
- [Theming](docs/theming.md), [Icons](docs/icons.md), [Blocks](docs/blocks.md), [Analytics](docs/analytics.md), [i18n](docs/i18n.md), [QR](docs/qr.md).
- [Component reference](docs/components/button.md): usage, attributes, events and accessibility per component.
- [Component Authoring](docs/component-authoring.md): the `src/components/<name>/` contract (repo only, not published).

`docs/*.md` and `docs/components/` also ship in the npm package, so the raw Markdown is reachable from a CDN, for example `https://cdn.jsdelivr.net/npm/nodexui@1.3.1/docs/usage-guide.md`. `docs/v2/` and `docs/component-authoring.md` are not published.

## Development

```sh
npm install
npm run build
npm run dev
```

Open `examples/index.html` through the dev server. It is the single shell for docs, the component gallery, blocks, icons and charts, routed by hash (`#/docs`, `#/components`, `#/blocks`, `#/icons`, `#/chart`). Standalone demos: `examples/apidoc/`, `examples/analytics/` and `momo_demo/` (static `b-ui` admin page).

Lucide's full SVG set is built in: `<span b-icon="search"></span>` in HTML or `thebase.icons.lucide.url("search")` from JavaScript. Builds publish the SVGs to `dist/icons/lucide/` and an index at `dist/icons/lucide.json`.

| Script | Purpose |
| --- | --- |
| `npm run dev` | Static server for examples and built `dist/` assets |
| `npm run build` | Build CDN and npm artifacts into `dist/` |
| `npm run lint` | JavaScript and CSS policy checks |
| `npm run test` | Runtime tests |
| `npm publish` | Runs `prepublishOnly` (lint, test, build) first |

## Distribution

The npm package `nodexui` publishes the built `dist/` artifacts, `docs/`, `README.md`, `CHANGELOG.md` and `LICENSE`. Everything is minified, with no unminified twins. Deliberate separate files: `dist/nodexui.icons.esm.js` (2000+ generated icon components), the ApiDoc language logos in `dist/apidoc/img/`, the analytics, i18n and ApiDoc bundles, and `dist/bootstrap.bundle.min.js` (unmodified, for projects that vendor built-in JS themselves).

## License

MIT. See [LICENSE](LICENSE).
