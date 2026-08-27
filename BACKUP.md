# Backup method: git push via Desktop Commander + Windows Credential Manager

Use this only if `gh` is unavailable or unauthenticated. See `README.md` for the current
standard method. This path still works and is safe; it is simply not the default anymore.

## Why this works (Claude never touches a credential)

- Zoltan's Windows machine (`C:\Users\gdp`) has working GitHub auth configured locally:
  - Global git identity: `~/.gitconfig` → `user.name = Zoltan Gero`, `user.email = humorketing@gmail.com`
  - `credential.helper = manager` (Windows Git Credential Manager) supplies the GitHub token
    transparently on every `git push`. Claude never sees, types, or stores this token.
  - SSH keys also exist at `~/.ssh/id_ed25519` as a further fallback, but HTTPS + Credential
    Manager is the proven path.
- Desktop Commander runs natively on Zoltan's Windows machine, not in Claude's cloud sandbox.
  Any `git push` run through it inherits that machine's credentials automatically.
- Claude's own cloud sandbox has NO GitHub credentials and cannot push there. Don't retry push
  from the cloud sandbox — it always fails with "could not read Username". Use Desktop Commander.

## The rule

Claude pushes to GitHub itself, via Desktop Commander on Zoltan's machine. Never ask Zoltan to
push it himself.

## Procedure

1. Build and test the project as usual (write code, run tests, dry-run verify).
2. Zip the finished repo, excluding `.git`, `__pycache__`, `.pytest_cache`.
3. Get the zip onto Zoltan's machine (Cowork outputs folder or staged via the device bridge).
4. Create/locate the project folder (convention: a sibling folder per repo under the relevant
   project directory, e.g. `C:\Users\gdp\Documents\Claude\Projects\🗺️ GHL Agency\<repo-name>`).
5. Extract the zip into that folder:
   `powershell.exe -Command "Expand-Archive -Path '<zip-path>' -DestinationPath '<target-folder>' -Force"`
6. Run the git sequence in one call:
   `powershell.exe -Command "cd '<target-folder>'; git init; git checkout -b main; git remote add origin https://github.com/YogiZoli/<repo>.git; git add -A; git commit -m '<message>'; git push -u origin main"`
   This succeeds silently, no login prompt, because of the Credential Manager setup above.
7. Verify: push output should show `* [new branch] main -> main` (or an update line). If in
   doubt, run `git ls-remote origin`.
8. Clean up any temporary zip once confirmed live on GitHub.

## PowerShell gotcha

Passing `Where-Object { $_.Name ... }` through a `-Command` string can mangle the `$_` token.
Prefer `Get-ChildItem -Exclude a,b,c`, or use Desktop Commander's `start_search` /
`list_directory` tools instead of hand-rolled filter pipelines.
