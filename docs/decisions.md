# GambitCS — Decisions log

A short record of the technical choices made while building GambitCS: one entry each, newest first, with what was chosen, why, what else could have worked and what it means for the code. The full stack lives in `GambitCS_Tech_Decisions.md` §1, product behavior in `GambitCS_Decisions_Update.md`, and the data model's own decisions in `docs/DATA_MODEL.md`.

---

## 2026-11-10 — Local database: expo-sqlite + Drizzle ORM

*Logged on November 19, 2026 (Day 2), once the data model was written.*

**Decision.** The money data lives in SQLite on the phone through `expo-sqlite`, read and written with Drizzle ORM. Migration files are generated with drizzle-kit and applied at startup.

**Why.**
- **Relational data with real rules.** The data model has 17 tables linked by foreign keys, with `CHECK` rules, unique rules and a partial unique index (`docs/DATA_MODEL.md`). SQLite enforces them, so a wrong row can't be written even if a service has a bug.
- **One transaction per user action.** Every service writes all its rows or none (Move it to a goal writes a contribution, a decision and a decision-week row together). SQLite transactions give that directly.
- **Week and month questions are queries.** "Entries in this week", "settlements without an answer", "the newest version on or before this Monday" are short SQL queries on indexed columns.
- **Types from one place.** Drizzle's schema in `src/db/` is the single source of the row types, so the repositories can't drift from the tables (`docs/ARCHITECTURE.md` §2).
- **Live queries.** Drizzle's live queries refresh a screen when the data under it changes, so a saved expense shows on Home without manual refreshing.
- **Fits the account save.** The whole database is one file, so saving it to the account is one clean copy made with `VACUUM INTO` (Tech decisions §4).
- **Part of Expo.** `expo-sqlite` ships with the Expo SDK and works in development builds without extra native setup.

**Other options and why not.**
- *expo-sqlite without an ORM:* works, but every type and migration would be written by hand.
- *WatermelonDB or Realm:* built for syncing between devices, which GambitCS doesn't do (Tech decisions §2); heavier than needed.
- *AsyncStorage or MMKV:* key-value stores, with no transactions, foreign keys or queries across weeks.

**What it means for the code.**
- The database provider opens the connection with change listening on (needed for live queries) and runs `PRAGMA foreign_keys = ON` (SQLite ignores foreign keys without it).
- Every transaction, and the account copy, goes through the single write queue: expo-sqlite uses one connection (Tech decisions §2 and §4).
- Each rule in `DATA_MODEL.md` is declared in the Drizzle schema where Drizzle can express it; anything it can't goes into a hand-written migration, never into a screen.
- Row types come from the schema (`src/db/`); the app's own types (`src/types/`) are separate, and repositories turn one into the other.

---

## 2026-11-10 — Navigation: Expo Router

*Logged on November 19, 2026 (Day 2).*

**Decision.** Navigation uses Expo Router: file-based routes in `src/app/`, built on React Navigation.

**Why.**
- **The navigation map becomes folders.** The tabs, the stacks inside them, the sheets and Settings in `docs/ARCHITECTURE.md` §4 map one-to-one to route files, so the map and the code stay in step.
- **Every screen has a URL.** Opening Quick add from the reminder notification is a link to a route, with no hand-written linking setup.
- **React Navigation underneath.** Its stacks, tabs, modals and screen options are still available where a screen needs them.
- **The Expo default.** It's what the Expo SDK and its documentation are written around, which keeps setup and upgrades simple.

**Other options and why not.**
- *React Navigation on its own:* the same engine with more setup by hand, and nothing GambitCS needs that Expo Router lacks.

**What it means for the code.**
- Screens live in `src/app/` (`docs/ARCHITECTURE.md` §3), and each file is a route.
- Two things to check when navigation is built (Day 6): Settings sliding in from the left, with the swipe back in the same direction (a custom animation and gesture, Decisions update Stage 1), and Activity belonging to two tabs (tiny route files or shared routes, `docs/ARCHITECTURE.md` §6).
