# 🥫 PantryPal

A free, self-hosted household pantry tracker with barcode scanning, expiry date tracking, and a smart shopping list. Inspired by [Grocy](https://grocy.info), but simpler and runs entirely in your browser.

**Built with:** Plain HTML/CSS/JS · Supabase (free database) · GitHub Pages (free hosting) · QuaggaJS (barcode scanning) · Open Food Facts API (product lookup)

---

## ✨ Features

- 📦 Track sealed stock and open items separately
- 📷 Barcode scanner using your phone camera — auto-fills product details
- ⭐ Priority items — flag essentials so they always appear on your shopping list
- 📅 Expiry date tracking with warnings for items expiring within 7 days
- 🛒 Auto-generated shopping list grouped by priority, colour coded by urgency
- 🖨️ Printable shopping list
- 📊 Full history log for every item — see trends over time
- 🔍 Search and filter by status, priority, or expiry
- 👫 Shared between multiple people — one login, same data everywhere

---

## 🚀 Setup Guide

This takes about 10–15 minutes and is completely free.

### What you'll need
- A free [GitHub](https://github.com) account (for hosting)
- A free [Supabase](https://supabase.com) account (for the database)
- Any modern browser (Chrome recommended for barcode scanning)

---

### Step 1 — Get the code

1. Download `index.html` from this repository
2. That's it — the entire app is one file

---

### Step 2 — Set up Supabase

Supabase is a free database service. Your pantry data lives here.

1. Go to [supabase.com](https://supabase.com) and sign up for a free account
2. Click **New Project**, give it a name (e.g. `pantrypal`), set a password, choose a region near you
3. Wait about 2 minutes for it to spin up

**Create the database tables:**

4. In your Supabase project, click **SQL Editor** in the left sidebar
5. Click **New query**
6. Paste the following and click **Run**:

```sql
create table items (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  brand text,
  category text,
  barcode text,
  emoji text default '🥫',
  qty_stocked int default 1,
  qty_open int default 0,
  expiry_date date,
  priority boolean default false,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

create table history (
  id uuid primary key default gen_random_uuid(),
  item_id uuid references items(id) on delete cascade,
  action text,
  note text,
  created_at timestamptz default now()
);

alter table items disable row level security;
alter table history disable row level security;
```

**Get your API keys:**

7. Go to **Settings → API** in the left sidebar
8. Copy two things:
   - **Project URL** — looks like `https://xxxxxxxxxx.supabase.co`
   - **anon / public key** — a long string starting with `eyJ...`

**Update the HTML file:**

9. Open `index.html` in any text editor (Notepad, VS Code, etc.)
10. Near the top of the `<script>` section, find these two lines:
```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';
```
11. Replace the placeholder values with your actual URL and key
12. Save the file

> **Note:** The anon/public key is safe to include in the file — it's designed to be public-facing. Never use the `service_role` key here.

---

### Step 3 — Host on GitHub Pages

GitHub Pages lets you host the app for free at a permanent URL.

1. Go to [github.com](https://github.com) and sign in
2. Click the **+** icon → **New repository**
3. Name it `pantrypal`, set it to **Public**, tick **Add a README**
4. Click **Create repository**
5. Click **Add file → Upload files**
6. Upload your `index.html` file — **make sure it's named exactly `index.html`**
7. Click **Commit changes**

**Enable GitHub Pages:**

8. Go to **Settings** in your repository (top tab)
9. Click **Pages** in the left sidebar
10. Under **Source**, select **Deploy from a branch**
11. Under **Branch**, select `main`, folder `/ (root)`
12. Click **Save**

After about 60 seconds your app will be live at:
```
https://yourusername.github.io/pantrypal
```

---

### Step 4 — Create your account

1. Open your GitHub Pages URL in a browser
2. Click **Create Account**, enter an email and password (minimum 6 characters)
3. Then click **Sign In** with the same details

> **Important:** By default Supabase requires email confirmation. To skip this (recommended for a private household app):
> - In Supabase go to **Authentication → Providers → Email**
> - Turn off **Confirm email**
> - Save

---

### Step 5 — Add to your home screen (optional but recommended)

On **iPhone:** Open the app in Safari → tap the Share button → **Add to Home Screen**

On **Android:** Open the app in Chrome → tap the three-dot menu → **Add to Home Screen**

This makes it feel like a native app.

---

### Sharing with others in your household

Everyone uses the **same email and password**. On each new device:

1. Open the GitHub Pages URL
2. Sign in with your shared credentials

All data is shared in real time.

---

## 📱 How to use PantryPal

### Adding items

**By barcode (recommended on phone):**
1. Tap the 📷 camera icon in the top right
2. Point your camera at any barcode
3. The app looks up the product automatically via Open Food Facts
4. Adjust details and quantities, then tap **Add to Pantry**

**Manually:**
1. Tap the **+** button (bottom right)
2. Fill in the name (required), brand, and category
3. Set how many are **sealed/stocked** and how many are currently **open**
4. Optionally add an expiry date and toggle **Priority** if it's an essential item
5. Tap **Add to Pantry**

### Tracking stock

Each item card shows two numbers:
- 📦 **Sealed** — unopened units in the cupboard
- 🔓 **Open** — units currently in use

Use the quick action buttons on each card:

| Button | What it does |
|--------|-------------|
| +1 📦 | You bought more — adds to sealed stock |
| 🔓 Open | You opened one — moves 1 from sealed to open |
| ✅ Done | You finished one — removes 1 from open |

### Status colours

| Colour | Meaning |
|--------|---------|
| 🟢 Green | Well stocked (2+ sealed) |
| 🟠 Orange | Low stock (only 1 sealed left) |
| 🟡 Yellow | Nothing sealed, but something open |
| 🔴 Red | Completely empty |

### Shopping list

Tap the 🛒 icon in the top right. The list auto-generates based on:
- Items that are **empty** → shown as 🔴 BUY NOW
- Items with only **1 left** → shown as 🟠 BUY SOON
- **Priority items** → always shown regardless of stock level

Tap **🖨️ Print / Save as PDF** for a clean printable version.

### Expiry dates

- Add an expiry date when creating or editing an item (always optional)
- Items expiring within **7 days** get an 🟠 warning badge
- **Expired** items get a 🔴 badge
- Use the **⚠️** filter to see all expiring/expired items at once

---

## 🔄 Updating the app

When a new version of `index.html` is available:

1. Go to your GitHub repository
2. Click on `index.html`
3. Click the **pencil (✏️) edit** icon
4. Select all the old code and delete it
5. Paste in the new code
6. Click **Commit changes**

> Your database data is completely separate from the HTML file. Updating the app never affects your pantry entries or history.

---

## 🛠️ Tech stack

| Component | Service | Cost |
|-----------|---------|------|
| Hosting | GitHub Pages | Free forever |
| Database | Supabase (PostgreSQL) | Free up to 500MB |
| Barcode scanning | QuaggaJS (open source) | Free |
| Product lookup | Open Food Facts API | Free |

---

## 📄 Licence

MIT — free to use, modify, and share. Inspired by the excellent [Grocy](https://grocy.info) project.
