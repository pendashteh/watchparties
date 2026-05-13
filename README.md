# Watch Parties

Lightweight static event page for watch parties. Visitors enter a party code on the home screen to reach a landing page with streaming links, attendance options, and event details.

All content is driven by two JSON files — no code changes needed to add organisations or events.

## How it works

- Home screen: code entry field
- Entering a valid code navigates to that party's page (`/<code>`)
- Party view is fully rendered at load time from `site.json` + `parties.json`

## Organisations (`site.json`)

Add organisations to the `entities` object. Each entry is keyed by a slug you choose (e.g. `inspire9`, `electron-workshop`):

```json
"my-org": {
  "name": "My Org",
  "url": "https://myorg.example",
  "logo": "logos/my-org.svg",
  "logoHeight": 28,
  "logoBg": "#ffffff",
  "logoPadding": "4px 10px",
  "mark": "MO",
  "markBg": "#c9a227",
  "markColor": "#0d1f3c"
}
```

`logo` is preferred; the `mark` box is the fallback when no logo is provided. `logoBg` wraps the logo in a coloured pill — useful for dark-background logos.

## Watch parties (`parties.json`)

Add an object to the `parties` array. Org references are keys that resolve from `site.json`:

| Field | Type | Notes |
|---|---|---|
| `code` | string | URL slug and entry code (e.g. `budget-2026`) |
| `organizer` | entity key | Shown in the party topbar |
| `sponsor` | entity key | Shown in the footer |
| `sponsorLabel` | string | e.g. `"eVenue sponsored by"` |
| `sponsorRole` | string | e.g. `"eVenue sponsor"` |
| `pageTitle` | string | Browser tab title |
| `title` | string[] | Title lines — rendered as `<br>` segments |
| `brand` | string | Name shown after `@` in the hero |
| `badges` | array | `{ text, style }` — style is `"filled"` or `"outline"` |
| `subtitle` | HTML string | Hero paragraph — supports `<strong>` etc. |
| `stream` | object | `url`, `label`, `displayUrl[2]`, `primaryCta`, `joinCta`, `mobileSub`, `note` |
| `externalLinks` | array | Extra CTA links in the hero row |
| `topbarLinks` | array | Nav links in the party topbar |
| `times` | array | Schedule entries — `{ type, label, start, url? }` (see below) |
| `sections.attendance` | string | Section heading |
| `attendance` | array | How-to-attend cards (see below) |
| `about` | object | `title`, `paragraphs[]`, `features[]` |
| `footer` | object | `org`, `orgUrl`, `address`, `extraLinks[]` |

**Times entry:**
```json
{ "type": "live", "start": "2026-05-13T19:30:00+10:00" }
```
`type` controls badge colour: `"live"` (red, animated), `"replay"` (blue), `"playback"` (gold). The display label is derived from `start` — timezone abbreviation inferred from the UTC offset. `url` is optional — overrides the venue URL for that specific time slot.

**Attendance card:**
```json
{
  "variant": "members",
  "icon": "🍳",
  "title": "Kitchen",
  "paragraphs": ["HTML string"],
  "tag": "Members Only"
}
```
`variant` controls styling: `"members"` (gold tint) or `"online"` (blue tint).

Fields marked "HTML string" support inline markup — write them as trusted developer content only.

## Running locally

Needs a local server (both JSON files are loaded via `fetch`):

```bash
python3 -m http.server 8080
# or
npx serve .
```

Then open `http://localhost:8080`.

## Deploying

Drop all files on any static host (Netlify, Vercel, GitHub Pages, nginx). No build step.

For clean URLs (`/budget-2026` → `index.html`), configure your host to serve `index.html` for all routes. On Netlify:

```
/*  /index.html  200
```

## Files

| File | Purpose |
|---|---|
| `index.html` | Single-page app — shell, styles, routing, rendering |
| `site.json` | Entity registry — organisations with name, URL, logo |
| `parties.json` | Watch party definitions — reference entities by key |
| `logos/` | Downloaded logo assets |
