# GitHub SOP

Canonical, single source of truth for how Claude does GitHub work on Zoltan's (YogiZoli) account.
This repo replaces the older Google Docs SOPs. Edit this repo when the procedure changes; do not
maintain the Drive versions as the primary copy.

**Status:** ACTIVE, STANDARD. Applies to every Claude/Cowork session that manages GitHub repos on
this account.

**Backup / fallback method:** git push via Desktop Commander + Windows Credential Manager. Still
valid, use it only if `gh` is unavailable or unauthenticated. See `BACKUP.md`.

## The rule

Claude does GitHub work itself, on Zoltan's Windows machine, via Desktop Commander (the
`mcp__remote-devices__Desktop_Commander__*` tools, running `gh`/`git` in a Windows shell on the
connected device). Never tell Zoltan to do it in the browser or push it himself. Claude never
sees, types, or stores a token.

## Why gh is the standard

The old SOP covered only `git push`. `gh` covers everything git alone cannot, all from the CLI
with no browser:

- Repo visibility (public/private): `gh repo edit YogiZoli/<repo> --visibility private --accept-visibility-change-consequences`
- Create a repo and push in one step: `gh repo create YogiZoli/<repo> --private --source=. --push`
- Releases, PRs, issues, repo settings. `gh` also configures git auth for pushing
  (`gh auth setup-git`), so it replaces the credential-manager dance too.

## Setup (already done 2026-08-26, one time)

- `gh` installed at `C:\Program Files\GitHub CLI\gh.exe` (winget: `GitHub.cli`).
- Authenticated as **YogiZoli**, stored in the Windows keyring, token scopes include `repo`.
  Persists across sessions.
- Verify any time: `& "C:\Program Files\GitHub CLI\gh.exe" auth status`

## The procedure (every session)

1. Reference `gh` by full path or ensure it is on PATH: `$gh = "C:\Program Files\GitHub CLI\gh.exe"`.
2. Confirm auth: `& $gh auth status`. If not logged in, run
   `& $gh auth login --hostname github.com --git-protocol https --web` and relay the one-time
   code to Zoltan (he confirms once in the browser).
3. Do the work: repo admin with `gh repo ...`, code push with normal `git` (after
   `& $gh auth setup-git`).
4. Verify: `& $gh repo view YogiZoli/<repo> --json name,visibility` etc.
5. NEVER run `gh`/`git` from Claude's cloud/Linux sandbox — no credentials there. Always via
   Desktop Commander on the Windows machine.

## Railway safety note

Making a repo private does NOT break Railway. The GitHub App keeps its per-repo access, so
connected services keep deploying. After a visibility change on a Railway-linked repo, trigger
one redeploy and confirm SUCCESS (proven 2026-08-26 for commentclient, publishing-dreamteam,
ghl-bulkgate).

## Common commands cheat sheet

- List repos + visibility: `& $gh repo list --limit 200 --json name,visibility`
- Make private: `& $gh repo edit YogiZoli/<repo> --visibility private --accept-visibility-change-consequences`
- Make public: `& $gh repo edit YogiZoli/<repo> --visibility public --accept-visibility-change-consequences`
- New private repo from current folder: `& $gh repo create YogiZoli/<repo> --private --source=. --push`
- Create a release: `& $gh release create v1.0.0 --notes "..."`

## Change log

- 2026-08-27: Migrated canonical SOP from Google Docs to this repo (github.com/YogiZoli/GITHUB-SOP).
  Drive doc is now a read-only mirror; older Drive SOPs banner-marked non-canonical.
- 2026-08-26: `gh` adopted as the standard tool, replacing git-push-only backup method.
