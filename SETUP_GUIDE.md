# My Spending Dashboard — Full Setup Guide

This guide takes you from zero to a live, installable app on your phone that
reads your Google Sheet, shows a debt-payoff projector, and lets you create new
months.

There are two capabilities with different setup needs:
- **Viewing** (dashboard, charts, debt projector) — works with the API key only.
- **Creating months** (writing to the sheet) — needs a one-time Google sign-in
  setup (OAuth Client ID).

---

## Safety design (how your data is protected)
- Viewing is strictly read-only.
- Creating a month is additive-only: it duplicates your "Template" tab into a
  brand-new tab and writes only into the line-item amount cells. It NEVER edits,
  overwrites, or deletes any existing month, and never writes to a formula cell.
  All totals compute themselves.

---

## Step 1 — Create the GitHub repository

1. Go to https://github.com/new
2. Repository name: `spending` (this becomes part of your URL)
3. Visibility: **Public** (GitHub Pages requires Public on free accounts)
4. Click **Create repository**

Your live URL will be:
```
https://YOURGITHUBNAME.github.io/spending/
```
Replace `YOURGITHUBNAME` with your GitHub username.

---

## Step 2 — Upload the app files

1. Unzip `spending-pwa.zip` on your computer.
2. In the new repo, click **Add file → Upload files**.
3. Drag in the files from INSIDE the `spending-pwa` folder (the files at the repo
   root — not the folder itself):
   - `index.html`
   - `manifest.json`
   - `sw.js`
   - `icon-192.png`
   - `icon-512.png`
   - `README.md` (optional)
4. Click **Commit changes**.

---

## Step 3 — Turn on GitHub Pages

1. Repo **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch = `main`, Folder = `/ (root)`, click **Save**
4. Wait ~1 minute, then open `https://YOURGITHUBNAME.github.io/spending/`

At this point Viewing works (dashboard, charts, debt projector). Only "New month"
still needs Step 4.

---

## Step 4 — OAuth Client ID (needed only to create months)

Writing to a sheet cannot use an API key; Google requires OAuth sign-in.

1. Go to https://console.cloud.google.com/apis/credentials
2. If prompted, configure the **OAuth consent screen**:
   - User type = **External**
   - Add an app name and your email
   - Under **Test users**, add your own Google address
   - Save (leaving it in "Testing" mode is fine for personal use)
3. **Create Credentials → OAuth client ID**
4. Application type = **Web application**
5. Name = anything (e.g. "Web client 1")
6. Under **Authorized JavaScript origins**, click **+ Add URI** and enter your
   origin (origin only — no path, no trailing slash):
   ```
   https://YOURGITHUBNAME.github.io
   ```
   (Optional, for local testing: also add `http://localhost:8000`)
7. Leave **Authorized redirect URIs** EMPTY (the app uses the token flow).
8. Click **Create**.
9. Copy the **Client ID** shown in the popup. It looks like:
   ```
   1234567890-abcdef....apps.googleusercontent.com
   ```

---

## Step 5 — Paste the Client ID into the app

1. In your GitHub repo, open `index.html` and click the pencil (Edit).
2. Find this line near the top of the script:
   ```js
   const CLIENT_ID = 'PASTE_YOUR_OAUTH_CLIENT_ID_HERE';
   ```
3. Replace the placeholder with your Client ID:
   ```js
   const CLIENT_ID = '1234567890-abcdef....apps.googleusercontent.com';
   ```
4. Commit changes. Wait ~1 minute for Pages to redeploy.

---

## Step 6 — Install on your phone

- **iPhone (Safari):** open the URL → Share → Add to Home Screen
- **Android (Chrome):** open the URL → menu → Install app

---

## Using the app

### Tabs
- **Overview** — averages, income vs spending, category breakdown
- **Monthly** — tap any month for its donut + split
- **Debt** — live avalanche payoff projector (editable APRs, extra-payment slider)
- **Trends** — surplus/deficit, necessary expenses, cumulative savings

### Create a new month
1. Tap **+ New month**
2. Sign in with Google (once per session)
3. Name the month (e.g. `AUG 2026`)
4. Adjust amounts in each area: Main spending, Optional, Savings.
   Car is toggled OFF by default — flip it on only if you need it.
5. Tap **Create month** — the new tab appears automatically.

### Debt projector
- APRs default to 24% (loans) / 28% (cards). Edit them in the Debt tab; your
  changes are saved on your device.
- Drag the extra-payment slider to see your debt-free date and interest saved.
- For accurate numbers, plug in your real statement APRs.

---

## Reference values (your current data)

### Editable cell map (Template tab)
| Area          | Amount cells |
|---------------|--------------|
| Income        | D2:D6        |
| Main spending | D11:D32      |
| Optional      | G11:G13      |
| Savings       | J11:J17      |
| Car           | M11:M17      |

All totals (D33, G14, G15, J18, M19, M20, the Category table) are formulas and
are never written by the app.

### Your debts (from the Debt tab)
| Debt         | Balance  | Monthly | Type    |
|--------------|----------|---------|---------|
| Monzo loan   | £16,337  | £408    | Fixed   |
| Flex         | £2,682   | £140    | Fixed   |
| Yonder       | £4,006   | £185    | Minimum |
| Capital One  | £601     | £23     | Minimum |
| **Total**    | £23,626  | £756    |         |

At £756/mo (avalanche), debt-free in ~51 months. Adding ~£200/mo cuts it to ~35
months and saves ~£4,900 in interest (using 24%/28% estimated APRs).

---

## Key values to keep handy
- Spreadsheet ID: `1Ca646hLsA4eZOHWf0eXITGnEmhOapd7XTAqaCnElCEM`
- API key (read-only, already in the app): in `index.html` as `API_KEY`
- Client ID: paste into `index.html` as `CLIENT_ID` (Step 5)

### Security note
The API key sits in the page source. Since it's a read-only key on a public
sheet that's low risk, but you should restrict it in the Google Cloud Console
(APIs & Services → Credentials → your key → API restrictions → restrict to
"Google Sheets API"). That way it can't be used for anything else even if seen.
