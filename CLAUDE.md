# Brooks OS — Project Context

## What this is
A mobile-first personal execution app built as a single HTML file, deployed on Vercel.
A Vercel serverless function (`api/notion.js`) proxies Notion API calls to bypass browser CORS.

## File structure
```
/index.html       — the full app (HTML + CSS + JS, single file)
/api/notion.js    — Vercel serverless function, Notion sync proxy
/vercel.json      — routing config
/CLAUDE.md        — this file
```

## Tech stack
- Vanilla JS, no frameworks, no build step
- localStorage for persistence (key: `brooksOS_v4`)
- Vercel for hosting + serverless functions
- Notion API for data sync

## Notion databases (already configured)
| Database | ID |
|---|---|
| Habit Tracker (Daily Log) | `cc09ede4-409b-83e5-a68b-01541e51bba1` |
| Weekly Targets | `e7b91e7877a744a9bee0da5b452de06d` |
| Monthly Overview | `b2c9ede4-409b-8365-a799-815f8e31aeb4` |

Notion workspace page: https://www.notion.so/7769ede4409b8364835c018bf058a7a4

## App structure — 5 tabs
1. **Home** — progress ring, focus card (main focus + next action), Start Focus, Low Energy, Plan Tomorrow, Close Out Day
2. **Habits** — 7 Daily Non-Negotiables (checkboxes + streaks) + 3 Weekly Targets (tap to increment)
3. **Tasks** — add/edit tasks with next action, priority, energy; Today + High Priority default filters
4. **Focus** — task selector, countdown timer, distraction log, session reflection
5. **Brain Dump** — free capture, categories (Task/Idea/Reminder/Worry/Decision/Park), convert to task

## Daily Non-Negotiables (habits)
Keys used in localStorage: `bible`, `pray`, `water`, `clean`, `focus`, `action`, `sleep`
Win thresholds: 70% = Win The Day, 85% = Great Day, 100% = Elite Day

## Weekly Targets
- Workout (target: 4x/week) — key: `workout`
- Trading Review (target: 3x/week) — key: `trading`
- Deep Backtesting (target: 2x/week) — key: `backtest`
Resets Monday. Stored in `S.weekly[weekStartDate]`.

## Notion sync
- Settings screen has API key field + pre-filled DB IDs
- "Sync to Notion" button calls `/api/notion` (POST)
- Actions: `sync_habits` (upserts today's row), `sync_weekly` (upserts this week's row)
- Notion integration name: "Brooks OS"

## State shape (localStorage)
```js
S = {
  days:     { [dateStr]: { mainFocus, nextAction, priorities[], reminder, avoid, notes, successDef } },
  habits:   { [dateStr]: { bible, pray, water, clean, focus, action, sleep } },
  weekly:   { [weekStart]: { workout, trading, backtest } },
  dumps:    [{ id, ts, text, cat, converted? }],
  tasks:    [{ id, name, next, priority, status, energy, due, createdAt, startedAt? }],
  sessions: [{ id, taskId, ts, duration, finished, distractions, nextStep }],
  reviews:  { [dateStr]: { win, carry, habitPct, sessions, tasks, dumps } },
  events:   [{ type, ts, ref }],
  notion:   { apiKey, habitsDb, weeklyDb }
}
```

## Key design rules
- Mobile-first, dark mode, max-width 430px
- No frameworks, no build step — edit index.html directly
- Do NOT add complexity, dashboards, or project management features
- App should feel calm, fast, and low-friction
- User: Kerry Brooks — real estate investor/wholesaler, trades, backtests, wakes up early for gym
