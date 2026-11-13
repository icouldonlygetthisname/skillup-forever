# SkillUpForever (fork)

Fork of [cjber/skillup-forever](https://github.com/cjber/skillup-forever) (GPL-3.0), a WoW Forever (Interface 16001) addon. `origin` = github.com/icouldonlygetthisname/skillup-forever, `upstream` = cjber. Upstream's own conventions are in AGENTS.md; follow them for code meant to go upstream.

## Current state

No local fixes. `main` is upstream tag `v1.9.5` plus this file (synced 2026-11-13), and the game runs the plain CurseForge v1.9.5 release, with auto-update on.

## If a fix is needed again

1. `git fetch upstream --tags`, then branch from the latest release tag (e.g. `git switch -c local-fixes vX.Y.Z`), so the installed build matches a release, not unreleased `main`.
2. Make each fix its own commit with a test, so it can be cherry-picked onto `upstream/main` for a PR. Opening a PR upstream is outward-facing: ask first. Keep this file out of PRs.
3. Run the specs: `for s in tests/*_spec.lua; do luajit "$s" || exit 1; done`. LuaJIT is installed via winget (`C:\Users\HAL9000\AppData\Local\Programs\LuaJIT\bin\luajit.exe`). `locale_spec` needs Python, which isn't installed, so it fails here. Lua 5.1 can't run the specs (no `bit` library).
4. `E:\VScode\_tools\bin\luacheck.exe <changed files> --no-color -q`
5. Deploy: `powershell -ExecutionPolicy Bypass -File E:\VScode\_tools\deploy-skillup.ps1`. It packages HEAD like upstream's packager (drops `.pkgmeta` ignores, dotfiles and this file, sets the version to `<describe>-local`) and moves the old copy to `%TEMP%\forever-backup\`.
6. Turn CurseForge auto-update off for SkillUpForever while a local build is installed, or it gets replaced.
7. On each new upstream release, rebase the branch onto the new tag and drop commits upstream has absorbed. Once none are left, delete the branch and go back to CurseForge.

## Past fixes

| Fix | What | Upstream |
|---|---|---|
| item data no longer rebuilds every tracked route | `STALE.items` dropped `trackedNeeds`. Bag addons (Baganator/Syndicator) fire `ITEM_DATA_LOAD_RESULT` constantly, and every rebuild re-ran `RouteReagents` for each tracked route (50-80 ms frames). | Fixed upstream in v1.3.x (#506, and #526 for the tracker redraw). Our commit and the `local-fixes` branch were dropped on 2026-11-10. |
