# ChemTrack — Lab Inventory Management

A self-contained web app that runs entirely in your browser, backed by Google Sheets as a shared team database. No server, no installation, no accounts for your team — just two HTML files and a Google Sheet.

---

## Files

| File | Who uses it | Purpose |
|---|---|---|
| `index.html` | Lab manager | Full inventory management — add, edit, expiry tracking, QR codes, reorder log |
| `checkout.html` | Everyone | Lightweight usage logging — identify a chemical, enter amount, submit |

---

## Setting up `index.html`

### Step 1 — Open the app

Download `index.html` and open it in any browser. You can rename it (`yourLabInventory.html` or whatever you prefer). No installation needed.

### Step 2 — Get an Anthropic API key (for AI label scanning)

Go to [console.anthropic.com](https://console.anthropic.com) → sign up or log in → **API Keys → Create new key** → copy it (starts with `sk-ant-...`).

In the app → **Setup tab → Anthropic API key** → paste it → **Save**.

Each photo scan costs roughly $0.01–0.03. Set a monthly spend limit in the console as a safety net.

### Step 3 — Set up Google Sheets (shared team database)

1. Go to [sheets.google.com](https://sheets.google.com) → create a new blank spreadsheet
2. Go to **Extensions → Apps Script**
3. Delete any existing code, paste the script shown in the app's **Setup tab → Show Apps Script code**
4. Click **Save**, then **Deploy → New deployment**
5. Set: Type = **Web app**, Execute as = **Me**, Who has access = **Anyone** → click **Deploy**
6. Copy the Web App URL it gives you
7. Back in the app → **Setup tab → Apps Script web app URL** → paste it → **Connect**
8. Also copy your spreadsheet's URL from the browser address bar → paste it into **Google Sheet direct URL** → **Save**
9. Click **Sync all data now** to push any existing entries up

From this point, every chemical and every checkout log entry is saved to your Google Sheet in real time.

### Step 4 — Share with your team

The simplest option is to host `index.html` on **GitHub Pages** or **Netlify** by uploading the single file — free, takes two minutes, gives you a stable URL everyone bookmarks. Alternatively, share via Google Drive (they open it → "Open with" → browser).

Everyone who opens the same URL shares the same Google Sheet, so all entries are visible to the whole team instantly.

> **Keep your three credentials safe** — Apps Script URL, Sheet URL, and API key. Store them in a password manager or private note. If browser history is cleared, you'll need to re-paste them in the Setup tab. Your chemical data in Google Sheets is unaffected.

---

## Setting up `checkout.html` (for lab members)

This is a stripped-down page for logging chemical usage — no editing, no deleting, just identify and log. Before distributing it, the lab manager fills in a config block at the top of the file.

### Before distributing: fill in the config block

Open `checkout.html` in any text editor (Notepad, TextEdit, VS Code, etc.) and find this section near the top (~line 170):

```javascript
// ════════════════════════════════════════════════════════════════
// ADMIN CONFIGURATION
// Fill these in before distributing to lab members.
// ════════════════════════════════════════════════════════════════
var CONFIG = {
  APPS_SCRIPT_URL:   '',
  SHEET_URL:         '',
  ANTHROPIC_API_KEY: ''
};
```

Replace the empty strings:

| Field | What to paste | Where to find it |
|---|---|---|
| `APPS_SCRIPT_URL` | `https://script.google.com/macros/s/.../exec` | Same URL from Step 3 above |
| `SHEET_URL` | `https://docs.google.com/spreadsheets/d/.../edit` | Address bar when your sheet is open |
| `ANTHROPIC_API_KEY` | `sk-ant-api03-...` | [console.anthropic.com](https://console.anthropic.com) — leave `''` to disable AI scanning |

**Example:**
```javascript
var CONFIG = {
  APPS_SCRIPT_URL:   'https://script.google.com/macros/s/AKfycbXXXXXX/exec',
  SHEET_URL:         'https://docs.google.com/spreadsheets/d/1aBcDeFgHiJk/edit',
  ANTHROPIC_API_KEY: 'sk-ant-api03-XXXXXXXX'
};
```

If `ANTHROPIC_API_KEY` is left blank, AI photo scanning is disabled and users are told to use QR or manual search — everything else works normally.

### Distribute the file

Share the configured `checkout.html` however works for your lab — email, Google Drive, Slack. Team members open it in a browser; no setup required on their end.

For **QR scanning** to work, the file must be served over HTTPS. Hosting it on GitHub Pages alongside `index.html` is the easiest option. If opened as a local `file://` link, photo and search still work but QR scanning will not.

---

## Day-to-day workflow

### Adding a new chemical (lab manager, `index.html`)

Open the **Add tab**, take 1–4 photos of the label, hit **Identify with AI**, review the auto-filled fields, click **Save**. About 30 seconds per chemical.

If the app detects a possible duplicate (same name, CAS number, or catalog number), it will ask what you want to do:

- **Edit existing** — jump into editing the existing entry
- **View in inventory** — go to the inventory tab with that chemical highlighted
- **Add stock to existing** — increment the stock count on the existing entry (lot/arrival/expiry update from the new scan if provided)
- **Save as new entry anyway** — bypass the warning (useful for same chemical from different suppliers or grades)

### Logging usage (everyone, `checkout.html`)

1. **Identify the chemical** — three options:
   - **Photo** — photograph the label; AI reads the name/CAS and finds it in inventory
   - **QR scan** — point the camera at the ChemTrack QR sticker on the bottle
   - **Search** — type the name or CAS number
2. **Fill in details** — your name, stock/bottle number, amount used, purpose, notes
3. **Submit** — saved to the `Checkout Log` tab in Google Sheets

Usage can also be logged from `index.html` → **Checkout tab → Log usage**, which includes the same fields.

### Expiry and reordering

The **Expiry tab** flags chemicals expiring within 90 days (adjustable in Setup). The **Reorder tab** tracks which chemicals are due for reordering based on your order frequency settings.

### QR labels

Use the **QR tab** to generate and print labels — one per chemical, containing all key info. Stick them on bottles. Team members can scan with any phone camera. There's also a QR icon in the inventory table for quick access, and an **All QR sheet** button to print everything at once.

### Safety

Review the **chemical datasheet and Safety Data Sheet (SDS)** before using any chemical. Links to PubChem and ECHA datasheets are available in each chemical's detail window.

---

## Security notes

- The Apps Script URL acts like a shared editor link to your Google Sheet — anyone who has it can read and write your inventory. Treat it like a password.
- The Anthropic API key is tied to your billing. If it leaks, revoke it immediately at [console.anthropic.com](https://console.anthropic.com) and issue a new one.
- In `checkout.html`, both the Apps Script URL and API key are embedded in the file itself. The distributed file is suitable for internal lab use, not for posting publicly.
- There is no login system by design, keeping setup simple for teams of any size. If tighter access control is needed, this can be added — see the code comments.
- The app can be used for anything with a label, not just chemicals. PubChem and SDS features won't apply to non-chemical items, but everything else works normally.

---

*Built with [Claude.ai](https://claude.ai)*
