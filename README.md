# Meeting Action Tracker

A single-page tracker for actions agreed in meetings. Add an action with an owner and due date, tick it off under each person, and see open, done and overdue counts at a glance. Overdue actions are highlighted in red.

Data is stored live in Supabase (table `public.meeting_actions`), and changes sync to everyone with the page open.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: markup, styles and script. Loads `@supabase/supabase-js` from jsDelivr. |
| `vercel.json` | Static hosting settings for Vercel: security headers, and no caching of the page so updates show immediately. |

There is no build step and no dependencies to install.

## Deploy to Vercel

1. In Vercel, choose **Add New → Project** and import this GitHub repository.
2. Leave **Framework Preset** as **Other**. Leave Build Command and Output Directory empty.
3. Click **Deploy**.

Every push to `main` redeploys automatically.

## Supabase

The Supabase project URL and publishable key are set near the top of the script in `index.html`:

```js
var SUPABASE_URL = "https://szukysnapqpbyiyaunna.supabase.co";
var SUPABASE_KEY = "sb_publishable_…";
```

The publishable key is meant to be public. What it can do is controlled by row-level security on the table. The current policies allow anyone with the page to read, add, update and delete actions. Tighten them if the page should be limited to signed-in team members.

### Table schema

```sql
create table public.meeting_actions (
  id uuid primary key default gen_random_uuid(),
  text text not null,
  owner text not null,
  due date not null,
  done boolean not null default false,
  is_sample boolean not null default false,
  created_at timestamptz not null default now()
);
```

Realtime is enabled on the table so ticks and new actions appear for everyone without a refresh.
