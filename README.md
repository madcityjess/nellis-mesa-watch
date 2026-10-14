# Nellis Mesa Watch

A tiny installable web app (PWA) that shows your saved auction search terms filtered to the
**Mesa** location on [nellisauction.com](https://www.nellisauction.com), refreshed on demand.

**Live app:** https://madcityjess.github.io/nellis-mesa-watch/

- Runs entirely in your phone's browser — **no server, no crawler, no PC**.
- Queries Nellis's public Algolia search index live each time you open/refresh, so results are current.
- Only still-open auctions, sorted **ending soonest**. Estimated retail shown (live bid price isn't public).
- Tap any card to open that item on nellisauction.com.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The whole app (UI + search) |
| `manifest.webmanifest` | Makes it installable / "Add to Home Screen" |
| `sw.js` | Service worker: caches the app shell, keeps results live |
| `icon-192.png`, `icon-512.png` | App icons |

## Put it on your phone (free hosting, ~5 minutes)

### Option A — GitHub Pages (recommended)

1. On your phone or a computer, sign in to GitHub and create a **new repository**
   (e.g. `nellis-mesa-watch`), **Public**.
2. Upload the 5 files above into the repository root
   (github.com → your repo → **Add file → Upload files**).
3. In the repo: **Settings → Pages → Source: Deploy from a branch → Branch: `main` / root → Save**.
4. Wait ~1 minute, then open `https://<your-username>.github.io/nellis-mesa-watch/`.
5. In Chrome: menu **⋮ → Add to Home screen**. Launch it like an app.
6. To change your search terms later, just edit them **inside the app** (they're saved on the phone).

### Option B — Netlify Drop (no account steps, quickest)

1. Go to <https://app.netlify.com/drop> and drag the `nellis-mesa-watch` folder in.
2. Open the URL it gives you, then **Add to Home screen**.

### Option C — no hosting at all

Save `index.html` (and the other files) to your phone's Downloads and open `index.html` in Chrome.
Everything works, but you won't get the "install as app" behaviour and updates require re-saving the file.

## Using it

- **Refresh** button re-queries live.
- **Search terms** box: add/remove terms (e.g. `Milwaukee`, `Raspberry Pi`). Saved on the phone.
- **Exact matches only** (on by default): a term must appear as a real word in the item's
  title or brand. Turn it off to search exactly like the Nellis site (which uses fuzzy/typo matching,
  so `lathe` also returns *leather* items).
- **Location** is fixed to `Mesa` (change `CONFIG.location` in `index.html` for another location such as `Phoenix`, `SW Las Vegas`, `Katy`, `Dallas`, `Denver`).

## How it works (for the curious)

Nellis's site is a front-end over an Algolia index. Their **public search-only** key is embedded in
their own page, and Algolia allows browser requests from any origin (`Access-Control-Allow-Origin: *`).
So the app talks to Algolia directly:

- Endpoint: `https://GL1QVP8R29-dsn.algolia.net/1/indexes/nellisauction-prd/query`
- Filter: `Location Name:Mesa`, open auctions only, non-sensitive only
- One query per term, merged and de-duplicated by item id, sorted by close time

If Nellis ever rotates that public key, the app automatically tries to recover the current key from
their website. If that fails, the fix is a one-line update in `index.html` (`CONFIG.apiKey`).

## Limitations

- **Current bid price / bid count are not public** (Nellis's price API requires a login), so only
  *estimated retail* is shown. The auction **end time** is shown instead, which matters more for not missing items.
- Results show up to 50 items per term (the soonest-ending ones).
