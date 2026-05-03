# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is the special `snaka/snaka` repository whose `README.md` is rendered as the user's GitHub profile page (https://github.com/snaka). There is no application code, build system, or test suite — every change here is a content/markup edit that becomes immediately public on the profile.

## Architecture

Only one moving part: **`README.md`** itself. Skill icons come from `skillicons.dev`, social/badge buttons come from `shields.io`, and stats / language / streak / activity-graph / WakaTime cards are all rendered by third-party README-as-a-service providers. Editing the profile means editing this file's markdown / inline HTML — there is no templating layer and no committed assets.

The WakaTime coding-stats card used to be a daily-regenerated `images/stat.svg` produced by a `Profile-Readme-WakaTime` GitHub Action; that workflow and the SVG were removed in favor of the hosted `github-readme-stats` WakaTime card, so the repo no longer receives "Automated Coding Activity Update" commits. The repo's `WAKATIME_API_KEY` secret is no longer used and can be deleted from repo settings.

## Self-hosted github-readme-stats

The WakaTime, GitHub Stats, and Top Languages cards in `README.md` point at a **personal Vercel deployment** of `anuraghazra/github-readme-stats`, not the public `github-readme-stats.vercel.app` instance. The public instance regularly hits Vercel function-invocation limits and returns `DEPLOYMENT_PAUSED` (HTTP 503), which silently breaks every card on the profile. Hosting our own copy isolates us from that.

- **Fork**: `snaka/github-readme-stats` (master branch auto-deploys)
- **Vercel project**: `github-readme-stats` under `snaka's projects` (Hobby plan)
- **Production domain in README**: `github-readme-stats-ashen-two-69.vercel.app`
- **Streak Stats** (`github-readme-streak-stats.herokuapp.com`) and **Activity Graph** (`github-readme-activity-graph.cyclic.app`) are still on their respective public instances — they are different projects, not part of github-readme-stats.

### PAT_1 rotation

The Vercel project has a single environment variable `PAT_1`, a **GitHub Fine-grained PAT** scoped to `Public Repositories (read-only)` on the `snaka` resource owner with no additional permissions. It exists only to lift the GitHub API rate limit from 60/hr (anonymous) to 5000/hr — it does not need write access anywhere. When it expires:

1. Generate a new fine-grained PAT at https://github.com/settings/personal-access-tokens/new with the same scope (Resource owner: `snaka`, Repository access: **Public Repositories (read-only)**).
2. Vercel project → **Settings** → **Environment Variables** → edit `PAT_1`, paste the new token, save.
3. Trigger a redeploy (Deployments → latest → ⋯ → **Redeploy**) so running functions pick up the new value. Without redeploy the old token stays cached in active functions until they idle out.

Symptoms of an expired/missing PAT: cards start returning rate-limit errors after burning through the 60/hr anonymous budget — usually visible as the GitHub Stats / Top Languages cards rendering as broken images while shields.io badges still work.

## Working in this repo

- There are no `build`, `lint`, or `test` commands — preview markdown rendering locally or push to a branch and view it on GitHub.
- Because every commit on `main` is visible on the public profile, prefer PRs for non-trivial changes.
- When adding/removing badges or stat cards, verify the external service is still alive before relying on it; several README-as-a-service providers in this file (Vercel, Heroku, Cyclic) have a history of going offline.
