# Asset Navigator

A self-contained, single-file data catalog browser for Collibra. It runs entirely inside the Collibra DGC application as a hosted HTML page — no external server, no build step, no dependencies to install.

---

## Features

- **Community/Domain/Asset tree** — Collapsible left-pane hierarchy of all communities, sub-communities, domains, and their assets. Each node displays a live asset count. Lazy-loads children on expand.
- **Global search** — Full-text asset search with a 50-result limit and paginated display.
- **Asset detail pane** — Click any asset to see its full attribute set, responsibilities (with inherited role support), and a visual relation diagram showing incoming and outgoing relationships. Clickable relation nodes navigate directly to the linked asset.
- **Relation diagram** — SVG-based three-column diagram with toggleable grouping by relation type.
- **Deep linking** — Asset selections are written to the URL hash (`#asset=<id>`), so links to specific assets can be bookmarked or shared.
- **Theme system** — Multiple built-in light and dark themes (DoD Dark, DoD Light, Minimal, and more). Theme preference is stored in `localStorage` and shared with the landing page.
- **Font customization** — User-selectable body and display fonts (Barlow, Inter, Roboto, Open Sans, and others) with an adjustable size scale slider (×0.85–×1.50).
- **Resizable split pane** — The divider between the tree and detail panes is draggable; position is persisted to `localStorage`.
- **Admin branding** — Organization name and app title can be configured via the admin panel and are stored in `localStorage` (key: `landingpage_admin_config`), shared with `landingpage.html`.

---

## How It Works

The page is hosted as a static file inside the Collibra DGC application. Because it is served from the same origin as DGC, it can make authenticated calls to:

- **Collibra REST API v2** (`/rest/2.0/…`) — communities, domains, assets, responsibilities, and CSRF token
- **Collibra GraphQL Knowledge Graph API** (`/graphql/knowledgeGraph/v1`) — asset details, attributes, and relations

Authentication uses the browser's existing Collibra session. No credentials are stored or transmitted separately.

---

## Installation

> **Important:** Always back up your Collibra environment before making changes.

### Step 1 — Create a backup

In the Collibra DGC console, create a backup that includes **customizations only** (not the full database). This backup is a `.zip` file.

### Step 2 — Add the file to the backup

Open the backup zip and add `assetNavigator.html` to the path:

```
dgc/images/assetNavigator.html
```

**Windows users:** Use [7-Zip](https://www.7-zip.org/) or an equivalent tool that lets you add files to a zip directly without extracting and recompressing. Extracting and recompressing a Collibra backup can corrupt it or cause checksum mismatches.

On Linux/macOS you can use:
```bash
zip backup.zip dgc/images/assetNavigator.html
```

### Step 3 — Upload and restore

Upload the modified backup through the Collibra DGC console (Settings → Backup/Restore → Restore) and apply it. The file will be available at:

```
https://<your-collibra-host>/resources/images/assetNavigator.html
```

---

## Post-Installation

Navigate to the URL above while logged in to Collibra. The page will use your existing session — no additional login is required.

### Optional: Add a navigation link

To make the page discoverable, add a link to it in your Collibra navigation bar or landing page.

---

## Configuration

All configuration is stored in the browser's `localStorage` and requires no server-side changes.

| Key | Scope | Description |
|---|---|---|
| `landingpage_admin_config` | Admin | Organization name, app title, default theme/fonts. Set via the admin panel. |
| `landingpage_user_prefs` | Per-user | Theme, font body/display, and font scale chosen by the user. |
| `assetNav.leftWidth` | Per-user | Last dragged width of the left tree pane. |

The `landingpage_admin_config` and `landingpage_user_prefs` keys are intentionally shared with `landingpage.html` so that theme and font preferences stay consistent across both pages.

### Changing the branding

Open the page, click the settings/theme button in the header, and use the Admin panel to set your organization name and app title. Changes take effect immediately and persist for all users of that browser.

---

## Browser Requirements

- A modern browser (Chrome, Edge, Firefox, Safari)
- An active Collibra DGC session
- JavaScript enabled

No external network access is required at runtime. Google Fonts are loaded from `fonts.googleapis.com` at page load for the font chooser — if your environment blocks external requests, fonts will fall back to system defaults gracefully.

---

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| Blank page / spinner that never resolves | Not logged in to Collibra, or CSRF token fetch failed. Log in to DGC and reload. |
| Tree shows no communities | Your Collibra user account does not have read access to any community. |
| Asset count shows `—` | Domain has no assets, or the asset count API call was throttled. Expand the domain to trigger a load. |
| Theme resets on reload | `localStorage` is blocked or in private/incognito mode. |
| Fonts not loading | Outbound requests to `fonts.googleapis.com` are blocked. Fonts fall back to system sans-serif automatically. |
