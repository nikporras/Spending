# Spending

Personal spending dashboard PWA. Reads a Google Sheet, charts months and
categories, projects debt payoff (avalanche), and can create new month tabs
via Google sign-in.

See [SETUP_GUIDE.md](SETUP_GUIDE.md) for full setup. Quick version:

1. Open `index.html` and replace the placeholders near the top of the script:
   - `API_KEY` — Google Sheets API key (read-only on a public sheet).
   - `CLIENT_ID` — OAuth Client ID (only needed for "+ New month").
2. Push to a GitHub repo and enable **Pages → Deploy from main / root**.
3. Open the live URL on your phone and install (Share → Add to Home Screen
   on iOS, menu → Install app on Android).

## Files
| File | Purpose |
|------|---------|
| `index.html` | The whole app — UI, charts, sheet IO, OAuth. |
| `manifest.json` | PWA install metadata. |
| `sw.js` | Service worker that caches the app shell. |
| `icon-192.png` / `icon-512.png` | App icons. |
| `SETUP_GUIDE.md` | Step-by-step setup. |

## Safety
- Viewing is API-key, read-only.
- "+ New month" only **duplicates** the `Template` tab into a new sheet and
  writes amounts into `D11:D32`, `G11:G13`, `J11:J17`, and (optionally)
  `M11:M17`. It never overwrites an existing month or a formula cell.
