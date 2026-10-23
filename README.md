# Milk Bank

A small iPhone app (PWA) for tracking breast milk: pumped (+), baby fed (−), donated (−), and tossed (−). The big number at the top is the current bank.

**Where the data lives:** a Google Sheet that you own. The phone keeps a working copy so the app opens instantly and still works offline. It never treats that copy as the record, though. Deleting the app, clearing Safari, or losing the phone loses nothing.

**Safety nets:**
- Deleting an entry only marks it `deleted = TRUE` in the Sheet. The row stays.
- Every action has an Undo button.
- Google Sheets keeps version history (File → Version history).
- Entries logged offline wait in a queue and sync when signal returns. Retries can never double-add an entry.

---

## Setup (about 15 minutes)

### 1. Google Sheet + backend
1. Create a new Google Sheet named **Milk Bank**.
2. Open **Extensions → Apps Script**. Delete the starter code and paste in all of `Code.gs`.
3. On line 17, change `SECRET` to your own long phrase, e.g. `oak-river-bottle-7731-snow`. Write it down; you'll type it into the phone once.
4. Pick `setup` in the function dropdown, click **Run**, and approve the permissions. Google will show an "unverified app" warning. That's expected because you wrote the app yourself: choose Advanced → Go to project.
5. Click **Deploy → New deployment → gear icon → Web app**.
   - Execute as: **Me**
   - Who has access: **Anyone**. This is required so the phone can reach it without a Google sign-in. The secret key is what keeps other people out.
6. Click **Deploy** and copy the **Web app URL** (it ends in `/exec`).

> ⚠️ **Gotcha:** if you edit `Code.gs` later, go to **Deploy → Manage deployments → pencil icon → Version: New version → Deploy**. If you skip that, the URL keeps running the old code.

### 2. Host on GitHub Pages
1. Create a repo, e.g. `milk-bank`. Upload `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`, `apple-touch-icon.png`. **Do not upload `Code.gs`**; it contains your secret.
2. Go to **Settings → Pages → Deploy from branch → main / root**.
3. The site will be at `https://<your-username>.github.io/milk-bank/`.

The repo can be public. The secret key is never in the repo; it lives only on the phone.

### 3. Install on her iPhone
1. Open the Pages URL in **Safari**.
2. Tap **Share → Add to Home Screen**.
3. Open the app **from the home screen icon**. Don't set it up inside Safari, because iOS keeps home-screen apps' storage separate from Safari's.
4. Paste the web app URL and secret key, then enter the current freezer total.

To add your own phone, repeat step 3. When it connects, it finds the existing bank and skips the starting-amount step.

---

## Using it
- **Pumped / Baby fed / Donated**: tap, choose an amount (quick chips or +/−), and save. The time defaults to now; change it to log something from earlier.
- **Spilled or expired milk?** subtracts milk that was lost.
- **Tap any entry** to see its details or delete it.
- **Settings → Recount**: after counting the bags in the freezer, enter the real total. The app logs a correction instead of rewriting history.
- **Summary tab** in the Sheet shows the same balance and lifetime totals.

## Updating the app
After you change `index.html`, bump `VERSION` in `sw.js` (e.g. `milkbank-v1.0.1`) so phones pick up the new version.
