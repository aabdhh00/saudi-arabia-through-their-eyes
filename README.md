# السعودية بعيونهم | Saudi Arabia Through Their Eyes

A bilingual (Arabic/English) storytelling platform with a live admin portal
backed by Supabase. The entire site is a single static file — `index.html` —
so it can be hosted for free on GitHub Pages with no backend server.

## What's already set up for you

The Supabase project and database were created and verified live (not just
described) as part of building this site. You don't need to run any SQL —
it's already there.

- **Project**: `saudi-arabia-through-their-eyes` (free tier, region `eu-central-1`)
- **Project URL**: `https://pioknzjcmghboqtsnyof.supabase.co`
- **Tables** (all created, Row Level Security enabled, public read + insert
  policies so the static site can work without a backend):

  | Table | Columns |
  |---|---|
  | `stories` | `id, title, author, category, content, image_url, created_at` |
  | `videos` | `id, youtube_url, embed_url, title, created_at` |
  | `social_posts` | `id, platform, url, created_at` |

The anon (publishable) key embedded in `index.html` is safe to expose in
client-side code by design — Supabase anon keys are meant to be public, and
access is governed by the RLS policies on each table, not by keeping the key
secret.

### Security note

Because this is a backend-less static site, the "Admin Portal" login
(`admin@saudieyes.com` / `admin123`) is a **UI gate only** — it decides
whether your browser *shows* the dashboard, not who is allowed to write to
the database. The `insert` policies on all three tables are public, so
technically anyone who has the anon key (visible in the page source) could
call the Supabase API directly and add rows without ever logging in through
the site. This is an inherent tradeoff of a no-backend architecture. If this
site starts handling anything sensitive, consider replacing the public
insert policies with a Supabase Edge Function that checks a real login
(Supabase Auth) before writing.

## Hosting on GitHub Pages (no coding required)

This repo is intentionally separate from the internal creative-pipeline
repo — it holds only the public-facing site (`index.html`, this README, and
the two logo images), so nothing about the client's pipeline, transcripts,
or strategy docs is exposed when this repo is made public for Pages.

1. This repository is already public and `index.html` sits at the repo
   root, so no restructuring is needed.
2. In this GitHub repo, go to **Settings → Pages**.
3. Under "Build and deployment", set **Source** to "Deploy from a branch".
4. Pick the branch this file lives on (`main`) and set the folder to
   **/(root)**.
5. Click **Save**. GitHub takes a minute or two to build, then shows a URL
   like `https://<your-username>.github.io/<repo-name>/` — that's your live
   site, no further path needed.

That's it — no build step, no npm install, nothing to compile. Any change
pushed to `index.html` goes live within a minute or two.

## Using the Admin Portal

1. Open the live site and click **"بوابتي" / "My Portal"** in the top
   navigation bar.
2. Log in with:
   - Email: `admin@saudieyes.com`
   - Password: `admin123`
3. A dashboard opens with three tabs:
   - **Add Story** — title, author, category, full text, optional cover
     image URL. Inserted straight into the `stories` table.
   - **Add Video** — paste any YouTube link (`watch?v=`, `youtu.be/`,
     `shorts/`, or an already-embed link); it's auto-converted to the
     correct `embed/` format and inserted into `videos`.
   - **Add Social Post** — pick Twitter (X) or Instagram and paste the post
     URL. It's inserted into `social_posts` and rendered using the official
     Twitter/Instagram embed widgets when they load successfully, or an
     elegant simulated card (same visual language as the rest of the site)
     if those third-party scripts fail to load or get blocked.
4. New content appears **instantly** for the admin who added it, and for
   every other visitor who loads the page afterward (it's fetched live from
   Supabase on every page load).

If the browser can't reach Supabase (offline, network issue, ad-blocker,
etc.), submissions are still saved to the browser's `localStorage` as a
backup and shown immediately, with a toast letting you know it wasn't synced
to the cloud yet.

## Updating the Supabase keys later

If you ever move this site to a different Supabase project (for example, a
paid tier, or a different Supabase account), update these two lines near the
top of `index.html`'s `<head>`:

```html
const SUPABASE_URL = "https://pioknzjcmghboqtsnyof.supabase.co";
const SUPABASE_ANON_KEY = "eyJhbGciOi...";
```

Get the new values from your Supabase project dashboard under
**Settings → API** — copy the **Project URL** and the **anon / public**
key (never the `service_role` key, which must stay secret and should never
be placed in client-side code). Then re-create the three tables using the
same schema listed above (see the SQL in the "What's already set up"
section, or re-run it from Supabase's SQL editor).

## Changing the admin password

The admin credentials are plain constants near the bottom of `index.html`:

```js
const ADMIN_EMAIL = "admin@saudieyes.com";
const ADMIN_PASSWORD = "admin123";
```

Edit these values directly. Remember this is a client-side-only gate (see
the security note above), so treat it as a convenience lock, not a real
authentication system.
