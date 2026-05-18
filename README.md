# ChemTrack — Lab Inventory Management

A self-contained web app that runs entirely in your browser, backed by Google Sheets as a shared team database. No server, no installation, no accounts needed for your team — just two HTML files and a Google Sheet.

---

## Files

| File | Who uses it | Purpose |
|---|---|---|
| `index.html` | Lab manager | Full inventory management — add, edit, expiry tracking, QR codes, reorder log |
| `checkout.html` | Everyone | Lightweight usage logging — identify a chemical, enter amount, submit |

---

## Setting up `index.html`

### Step 1 — Open the app

Download `index.html` and open it in any browser. You can rename it if you like. No installation needed.

### Step 2 — Get an Anthropic API key (for AI label scanning)

Go to [console.anthropic.com](https://console.anthropic.com) → sign up or log in → **API Keys → Create new key** → copy it (starts with `sk-ant-...`).

In the app → **Setup tab → Anthropic API key** → paste → **Save**.

Each photo scan costs roughly $0.01–0.03. Set a monthly spend limit in the console as a safety net.

### Step 3 — Set up Google Sheets (shared team database)

1. Go to [sheets.google.com](https://sheets.google.com) → create a new blank spreadsheet
2. Go to **Extensions → Apps Script**
3. Delete any existing code, paste the script shown in the app's **Setup tab → Show Apps Script code**
4. Click **Save**, then **Deploy → New deployment**
5. Set: Type = **Web app**, Execute as = **Me**, Who has access = **Anyone** → click **Deploy**
6. Copy the Web App URL it gives you
7. Back in the app → **Setup tab → Apps Script web app URL** → paste → **Connect**
8. Copy your spreadsheet's URL from the browser address bar → paste into **Google Sheet direct URL** → **Save**
9. Click **Sync all data now** to push any existing entries up

From this point, every chemical and every checkout log entry is saved to your Google Sheet in real time.

> **Updating from an earlier version?** If your Google Sheet already has data, the new Apps Script adds three tag columns (Tag1, Tag2, Tag3) after the Frequency column. Existing rows will have those cells blank — that's fine, the app handles it gracefully.

### Step 4 — Share with your team

Host `index.html` on **GitHub Pages** or **Netlify** by uploading the single file — free, takes two minutes, gives everyone a stable URL to bookmark. Alternatively, share via Google Drive (open → "Open with" → browser).

> **Keep your credentials safe.** Store the Apps Script URL, Sheet URL, and API key in a password manager or private note. If browser history is cleared, you'll need to re-paste them in Setup — your Google Sheets data is unaffected.

---

## Setting up `checkout.html` (for lab members)

This page is for logging chemical usage — no editing, no deleting. It contains **no sensitive information in the file itself**. Each person enters the connection details once on their own device, and the browser remembers them.

### What to send to your team

Share the `checkout.html` file (or a link to it if hosted). Then separately — in a message, email, or Slack — send them:

- **Apps Script URL** (required) — the same URL from Step 3 above
- **Anthropic API key** (optional) — only needed for AI photo scanning

### What each person does on first open

1. The **Setup screen** opens automatically on first use
2. They paste in the Apps Script URL (and optionally the API key)
3. Hit **Save & connect** — done. The browser remembers these values

The ⚙ icon in the top-right re-opens Setup if needed (e.g. after clearing browser history).

> For **QR scanning** to work, `checkout.html` must be served over HTTPS. Hosting it on GitHub Pages is the easiest option. Photo and search still work from a local file.

---

## Day-to-day workflow

### Adding a new chemical (`index.html`)

Open the **Add tab**, take 1–4 photos of the label, hit **Identify with AI**, review the auto-filled fields, and click **Save**. About 30 seconds per chemical.

**Tags** — each chemical can have up to three tags (Tag 1, Tag 2, Tag 3). Click any tag field to see suggested options, or type your own:

> `Powder` `Liquid` `Gas` `Solution` `Buffer` `Antibody` `Enzyme` `Kit` `Standard` `Solvent` `Acid` `Base` `Oxidizer` `Flammable` `Biological`

Tags are optional — leave them blank if not needed. They appear as blue badges in the inventory table and in the detail modal, and can be filtered using the **All tags** dropdown in the Inventory tab.

If the app detects a possible duplicate (same name, CAS, or catalog number), it asks what to do:

- **Edit existing** — jump into editing the existing entry
- **View in inventory** — go to inventory with that chemical highlighted
- **Add stock to existing** — increment the stock count (lot/arrival/expiry update from the new scan if provided)
- **Save as new entry anyway** — useful for same chemical from different suppliers or grades

### Logging usage (`checkout.html`)

1. **Identify the chemical** — three options:
   - **Photo** — photograph the label; AI reads the name/CAS and finds it in inventory
   - **QR scan** — point the camera at the ChemTrack QR sticker on the bottle
   - **Search** — type the name or CAS number
2. **Fill in details** — your name, stock/bottle number, amount used, purpose, notes
3. **Submit** — saved to the `Checkout Log` tab in Google Sheets

Usage can also be logged from `index.html` → **Checkout tab → Log usage**.

### Expiry and reordering

The **Expiry tab** flags chemicals expiring within 90 days (adjustable in Setup). The **Reorder tab** tracks chemicals due for reordering based on your order frequency settings.

### QR labels

Use the **QR tab** to generate and print labels — one per chemical, containing all key info. Stick them on bottles. Anyone can scan with a phone camera. **All QR sheet** prints everything at once. There's also a QR icon in the inventory table for quick access per chemical.

### Safety

Review the **chemical datasheet and Safety Data Sheet (SDS)** before using any chemical. Links to PubChem and ECHA are available in each chemical's detail window.

---

## Security notes

- The **Apps Script URL** acts like a shared editor link to your Google Sheet — anyone who has it can read and write your inventory. Treat it like a password.
- The **Anthropic API key** is tied to your billing. If it leaks, revoke it at [console.anthropic.com](https://console.anthropic.com) and issue a new one.
- Neither credential is embedded in `checkout.html` — the file itself is safe to share openly. Values are stored in each user's browser localStorage only.
- There is no login system by design, keeping setup simple for teams of any size (under 100).
- The app works for anything with a label, not just chemicals. PubChem and SDS features won't apply to non-chemical items, but everything else functions normally.

---

*Built with [Claude.ai](https://claude.ai)*
