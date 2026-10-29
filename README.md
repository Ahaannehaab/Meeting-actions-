# Meeting Action Tracker

A single-page tracker for actions agreed in meetings. Add an action with an owner and due date, tick it off under each person, and see open, done and overdue counts at a glance. Overdue actions are highlighted in red.

Team members sign in with a username and password (Supabase Auth). Actions are stored live in Supabase (table `public.meeting_actions`) and changes sync to everyone signed in.

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

## Users and sign-in

Passwords are stored by Supabase's built-in authentication in `auth.users` (hashed, never in this repo). Usernames are saved as `<username>@example.com` in Supabase Auth, and the page adds that suffix when someone signs in.

Starting accounts, all with the initial password `password`: `priya` (admin), `dan`, `marcus`. Everyone should change their password after first sign-in: **Account → Change your password**.

Admins can add users from **Account → Team**. That calls the `create_team_member` database function, which only admins can run.

## Supabase

The Supabase project URL and publishable key are set near the top of the script in `index.html`:

```js
var SUPABASE_URL = "https://szukysnapqpbyiyaunna.supabase.co";
var SUPABASE_KEY = "sb_publishable_…";
```

The publishable key is meant to be public. What it can do is controlled by row-level security: only signed-in users can read, add, update or delete actions, and the signed-out role has no access to the table.

### Tables

`public.profiles` links each Supabase Auth user to a username, display name and admin flag. A trigger creates the profile whenever a user is added.

`public.meeting_actions` holds the actions:

```sql
create table public.meeting_actions (
  id uuid primary key default gen_random_uuid(),
  text text not null,
  owner text not null,
  due date not null,
  done boolean not null default false,
  is_sample boolean not null default false,
  created_at timestamptz not null default now(),
  created_by uuid default auth.uid() references auth.users (id) on delete set null
);
```

Realtime is enabled on the table so ticks and new actions appear for everyone without a refresh.
