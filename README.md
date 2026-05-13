# Habit Tracker

A 30-day habit tracker with planner, journal, and optional cloud sync.

**Tech:** Vanilla JS, single-file HTML, Supabase (auth + sync)
**Privacy:** see [privacy.html](privacy.html)

## Features
- 30-day grid for daily habits
- Planner with archive
- Sleep & journal tracking
- Two themes (PROTOCOL 30 + Premium Dark)
- EN / RU interface
- Works offline (localStorage)
- Optional cloud sync via Supabase

## Run locally
Open `index.html` in any modern browser. No build step.

## Configure cloud sync
1. Edit `config.js` with your Supabase project URL and anon key.
2. Create a `user_data` table with the schema below in Supabase SQL Editor.

```sql
create table user_data (
    user_id uuid primary key references auth.users on delete cascade,
    state jsonb not null default '{}'::jsonb,
    updated_at timestamptz not null default now(),
    created_at timestamptz not null default now()
);

alter table user_data enable row level security;

create policy "users read own data" on user_data for select using (auth.uid() = user_id);
create policy "users insert own data" on user_data for insert with check (auth.uid() = user_id);
create policy "users update own data" on user_data for update using (auth.uid() = user_id);
create policy "users delete own data" on user_data for delete using (auth.uid() = user_id);
```

## Architecture
- Frontend: vanilla JS + CSS variables, single file (~100KB)
- Local storage: `localStorage` is always source of truth in-session
- Cloud sync: debounced (1s) push to Supabase, only when signed in
- Backend: single `user_data` table with `state jsonb`, row-level security

## License
MIT — see [LICENSE](LICENSE).
