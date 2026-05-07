# LabInventory: Lab inventory management 


## What this app actually is
It's a self-contained web app that runs entirely in your browser — no server needed. Data is saved locally in your browser's storage, and optionally synced to a shared Google Sheet that your whole team can view.


## Step 1 — Save the app as an HTML file
To deploy this for real: Download the index.html, save it as it is or change its name (maybe LabInventory.html) on your computer
That's a fully working app — you can open it in any browser right now


## Step 2 — Get your Anthropic API key (for photo AI)

Go to console.anthropic.com → sign up or log in
Go to "API Keys" → create a new key → Copy it (starts with sk-ant-...)

In the app → Setup tab → paste it under "Anthropic API key" → Save
(This is what powers the label-reading feature. Each photo scan costs roughly $0.01–0.03)


## Step 3 — Set up Google Sheets (for the shared team database)

Go to sheets.google.com → create a new blank spreadsheet
In that spreadsheet, go to Extensions → Apps Script
Delete any existing code, then paste the Apps Script code shown in the app's Setup tab → "Show Apps Script code"
Click Save (floppy disk icon), then Deploy → New deployment
Set type to Web app, execute as Me, access to Anyone → click Deploy
Copy the long URL it gives you
Back in the app → Setup tab → paste the URL → click Connect
Click Sync all data now to push any existing chemicals up

From this point, every chemical and every checkout log entry automatically posts a new row to your Google Sheet in real time.


## Step 4 — Share it with your people

The simplest option is to host the HTML file somewhere your team can reach it. You can put it on Google Drive and share the link (they open it, then "Open with" → browser), or host it for free on GitHub Pages or Netlify by uploading the single file. Anyone with the link opens the same app, and everyone's entries go into the same Google Sheet.
If you want everyone to see the live inventory (not just add to it), just share the Google Sheet link directly — it's a normal spreadsheet they can filter, sort, and view without needing the app.


## Day-to-day workflow for your team
When a new chemical arrives, one person opens the app, takes 1–4 photos of the label, hits "Identify with AI," reviews the auto-filled fields, and clicks Save. That's about 30 seconds per chemical.

When someone takes a chemical from the shelf, they go to the Checkout log tab, hit "Log usage," pick the chemical, enter their name and how much they used. This keeps a full audit trail of who used what.

The Expiry tab flags anything expiring within 90 days (adjustable) so you can reorder before you run out. The Reorder tab shows which chemicals are due for ordering based on your order frequency settings.

For the physical bottles, use the QR tab to print labels — stick one on each bottle, and anyone can scan it with their phone camera to instantly see all the chemical's details.


### For duplicates, it will trigger:
- Exact name match — "Ethanol" typed again when "Ethanol" already exists
- Fuzzy name match — "Ethanol absolute" when "Ethanol" exists, or vice versa
- CAS number match — same CAS number even if the name is spelled differently
- Catalog number match — same catalog # from any company

### Action choices:
- Edit this entry — jumps straight into editing the existing chemical
- View in inventory — goes to inventory tab with that chemical highlighted in amber
- Add stock to existing — increments the stock count on the existing entry (also updates lot/arrival/expiry from the new scan if provided), then discards the duplicate form
- Save as new entry anyway — bypasses the warning and saves it as a separate entry (useful for same chemical from different suppliers or different grades)


* coded by Claude.ai
