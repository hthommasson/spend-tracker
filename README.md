# Shared Ledger

A two-person budget register. Write in every expense and income by hand as it happens, and watch it against the monthly budget you set. Each person keeps their own column; a **Both** view combines them.

It works the moment it's online (data saved on that device). Turn on **sync** and both of you see each other's entries live, on every phone and desktop.

---

## Part 1 — Put it online with GitHub Pages

1. Create a new GitHub repository (name it anything, e.g. `ledger`).
2. **Add file → Upload files**, drag in `index.html`, and commit.
3. In the repo: **Settings → Pages**. Under *Source*, choose **Deploy from a branch**, set branch to **main** and folder **/ (root)**, then **Save**.
4. Wait ~1 minute. Your app is live at:
   `https://YOUR-USERNAME.github.io/YOUR-REPO/`

That URL is what you both open and install.

## Part 2 — Install it like an app

- **iPhone / iPad (Safari):** open the URL → Share → **Add to Home Screen**.
- **Android (Chrome):** open the URL → ⋮ menu → **Add to Home screen / Install app**.
- **Desktop (Chrome/Edge):** open the URL → the **install icon** in the address bar → Install.

It opens full-screen with its own icon. On first run, choose **Start fresh**, enter both names, and you're in.

## Part 3 — Turn on sync (optional, ~10 min, free)

Sync uses [Supabase](https://supabase.com), a free hosted database.

1. Sign in at supabase.com → **New project**. Give it a name and a database password, pick the free plan, and let it provision (~2 min).
2. Left sidebar → **SQL Editor → New query**. Paste everything below and click **Run**:

   ```sql
   create table if not exists ledger_entries (
     id text primary key,
     date text not null,
     person smallint not null,
     category_id text,
     amount numeric not null,
     note text default '',
     created_at bigint,
     updated_at bigint
   );

   create table if not exists ledger_config (
     id text primary key,
     data jsonb not null
   );

   alter table ledger_entries enable row level security;
   alter table ledger_config  enable row level security;

   create policy "app access" on ledger_entries for all using (true) with check (true);
   create policy "app access" on ledger_config  for all using (true) with check (true);

   alter publication supabase_realtime add table ledger_entries;
   alter publication supabase_realtime add table ledger_config;
   ```

3. Left sidebar → **Project Settings (gear) → API**. Copy the **Project URL** and the **anon public** key.
4. In the app: **Budget** tab → **Sync across devices** → paste both → **Turn on sync**. Your existing entries seed up to the cloud.
5. On the **second device**: open the same site, pick **Join existing** on the first screen, paste the *same* URL + key → **Connect & pull ledger**. Done — you're both live.

---

## A note on privacy

The **anon public** key is meant to live in the app's code, and since GitHub Pages serves your files publicly, anyone who views the page source can see it. The SQL above leaves security on but permissive, which means someone who found your project URL + key could read or change the data. For a personal budget on an obscure URL the practical risk is low — but if you want real protection (an email login so only the two of you can get in), ask and it can be added.

## Good to know

- **Editing conflicts:** if you both edit the *same* entry within moments of each other, the later save wins. Adding different entries never conflicts.
- **Offline:** entries you add offline are saved on the device and pushed the next time you're online and make a change.
- **Fonts:** need internet on first load; after that the app is fully cached by your browser.
- **Backup:** with sync on, Supabase holds your data. Without sync, it lives only in that browser — don't "clear site data" for it.
