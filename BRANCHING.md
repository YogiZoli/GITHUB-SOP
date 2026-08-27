# Branch strategy — CANONICAL

Part of the GitHub SOP. Canonical source: https://github.com/YogiZoli/GITHUB-SOP
Last updated: 2026-08-27

## The model: GitHub Flow

One permanent branch: `main`. Everything else is a short-lived branch for one
task, then deleted. No `dev`, no `test`, no `release` branches. The old
three-branch Git Flow (dev / test / main) is retired and must not be used.

Rationale: every app we ship is a continuously deployed web service. Railway
auto-deploys `main`. GitHub Flow matches that; Git Flow does not.

## Rules

1. `main` is always deployable. Railway deploys production from `main` on every
   merge. Never commit directly to `main`.
2. One task = one branch, branched from an up-to-date `main`.
3. Branch names: `feature/<short-name>`, `fix/<short-name>`, `chore/<short-name>`.
4. Develop and test locally first (run the app on localhost). Pushing a feature
   branch deploys nothing; Railway only watches `main`.
5. Merge to `main` only through a Pull Request, only after Zoltan approves.
6. Delete the feature branch after merge. Pull `main` locally to resync.
7. A pushed feature branch is also the off-machine backup of in-progress work.

## Persistent staging (optional, not default)

If a project genuinely needs a pre-production check on real infrastructure, add
exactly one more permanent branch, `staging`, wired to a separate Railway
environment. Flow becomes `feature/x` -> `staging` -> `main`. Do not add this
until a project actually needs it. Solo development usually does not.

## Standard commands

Start a feature:

	cd C:\Users\gdp\projects\<repo>
	git checkout main
	git pull
	git checkout -b feature/<short-name>

Work loop:

	git add -A
	git commit -m "<what it does>"

Open the PR:

	git push -u origin feature/<short-name>
	gh pr create --fill --base main

After approval, merge and resync:

	gh pr merge --squash --delete-branch
	git checkout main
	git pull

Railway redeploys `main` automatically. Verify:

	railway status

## Enforcement

Server-side branch protection (rulesets) on private repos requires GitHub Pro.
Until then, each local clone has a `pre-push` git hook that blocks a direct push
to `main`. It is machine-local and bypassable with `git push --no-verify`
(emergency only). To install the hook in a fresh clone, copy it from an existing
clone's `.git/hooks/pre-push` or recreate it from this SOP.

Hook body (`.git/hooks/pre-push`, must be executable):

	#!/bin/sh
	protected="main"
	while read local_ref local_sha remote_ref remote_sha
	do
		case "$remote_ref" in
			refs/heads/$protected)
				echo "BLOCKED: direct push to '$protected'. Use a feature branch + PR."
				echo "Emergency override: git push --no-verify"
				exit 1
				;;
		esac
	done
	exit 0

If GitHub Pro is adopted later, add a `protect-main` ruleset (require PR,
0 approvals, block deletion and force-push) and keep the hook as a local
early warning.

## Change log

- 2026-08-27: Adopted GitHub Flow. Retired the dev / test / main model. Added
  the local pre-push hook to commentclient and publishing-dreamteam.
