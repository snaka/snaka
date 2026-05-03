# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is the special `snaka/snaka` repository whose `README.md` is rendered as the user's GitHub profile page (https://github.com/snaka). There is no application code, build system, or test suite — every change here is a content/markup edit that becomes immediately public on the profile.

## Architecture

Only one moving part: **`README.md`** itself. Skill icons come from `skillicons.dev`, social/badge buttons come from `shields.io`, and stats / language / streak / WakaTime cards are all rendered by third-party services hosted on Vercel/Heroku/Cyclic (`github-readme-stats.vercel.app`, `github-readme-streak-stats.herokuapp.com`, `github-readme-activity-graph.cyclic.app`). Editing the profile means editing this file's markdown / inline HTML — there is no templating layer and no committed assets.

The WakaTime coding-stats card used to be a daily-regenerated `images/stat.svg` produced by a `Profile-Readme-WakaTime` GitHub Action; that workflow and the SVG were removed in favor of the hosted `github-readme-stats` WakaTime card, so the repo no longer receives "Automated Coding Activity Update" commits. The repo's `WAKATIME_API_KEY` secret is no longer used and can be deleted from repo settings.

## Working in this repo

- There are no `build`, `lint`, or `test` commands — preview markdown rendering locally or push to a branch and view it on GitHub.
- Because every commit on `main` is visible on the public profile, prefer PRs for non-trivial changes.
- When adding/removing badges or stat cards, verify the external service is still alive before relying on it; several README-as-a-service providers in this file (Vercel, Heroku, Cyclic) have a history of going offline.
