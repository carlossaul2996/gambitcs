# GambitCS

Local-first personal finance app for iOS: weekly budgets from three spending plans, plus a simulator that projects your year. Built with React Native, Expo, TypeScript and SQLite.

## Status

Work in progress. Currently building **v1.0 (core loop)**. See the [milestones](https://github.com/carlossaul2996/gambitcs/milestones) for what each release includes.

## What it does

- **Pick a plan:** Tight, Balanced or Easy. Choose how much to save each week, and every category adjusts to it.
- **A weekly budget that follows your bills:** rent, phone and other bills show in full in the week they're due, instead of being spread across the month.
- **A simulator for your year:** see what you'll save month by month and week by week, and change your plan from there.
- **Your data stays on your phone:** no bank connection. You log what you spend.

## Tech stack

- React Native + Expo, TypeScript in strict mode
- Expo Router for navigation
- NativeWind for styling
- expo-sqlite + Drizzle ORM for the local database
- i18next + expo-localization (English and Spanish)
- Jest for tests

## Running it locally

Coming soon. GambitCS runs as an Expo development build, and the setup steps will be added here.

## Docs

- [Architecture](docs/ARCHITECTURE.md): layers, folder structure and navigation map
- [Data model](docs/DATA_MODEL.md): every table and the rules that connect them
- [Decisions](docs/decisions.md): technical choices and why they were made

## License

© 2026 Carlos Martinez. All rights reserved.

This code is public so you can read it, but it isn't licensed for reuse.