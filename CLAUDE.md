# SkillUpForever (fork with local fixes)

Fork of [cjber/skillup-forever](https://github.com/cjber/skillup-forever) (GPL-3.0), a WoW Forever (Interface 16001) addon. `origin` = github.com/icouldonlygetthisname/skillup-forever, `upstream` = cjber. Upstream's own conventions are in AGENTS.md; follow them for code meant to go upstream.

## Branches

- `local-fixes`: the latest upstream release tag plus our commits. This is what's installed in game.
- Each fix is its own commit with a test, so it can be cherry-picked onto `upstream/main` for a PR. Opening a PR upstream is outward-facing: ask first.
- This file is a separate commit. Leave it out of PRs.

## Fixes

No local fixes right now: `local-fixes` is the upstream release plus this file, so plain CurseForge updates are fine.

| Fix | What | Upstream |
|---|---|---|
| item data no longer rebuilds every tracked route | `STALE.items` dropped `trackedNeeds`. Bag addons (Baganator/Syndicator) fire `ITEM_DATA_LOAD_RESULT` constantly, and every rebuild re-ran `RouteReagents` for each tracked route (50-80 ms frames). | Fixed upstream in v1.3.x (#506, and #526 for the tracker redraw). Our commit was dropped on 2026-11-10. |

## New upstream release

1. `git fetch upstream --tags`
2. `git rebase --onto vX.Y.Z <old tag> local-fixes` (or rebase onto the new tag). Drop any commit upstream has absorbed.
3. Run the specs: `for s in tests/*_spec.lua; do luajit "$s" || exit 1; done`. LuaJIT is installed via winget (`C:\Users\HAL9000\AppData\Local\Programs\LuaJIT\bin\luajit.exe`). `locale_spec` needs Python, which isn't installed, so it fails here. Lua 5.1 can't run the specs (no `bit` library).
4. `E:\VScode\_tools\bin\luacheck.exe <changed files> --no-color -q`
5. Deploy: `powershell -ExecutionPolicy Bypass -File E:\VScode\_tools\deploy-skillup.ps1`. It packages HEAD like upstream's packager (drops `.pkgmeta` ignores, dotfiles and this file, sets the version to `<describe>-local`) and moves the old copy to `%TEMP%\forever-backup\`.

CurseForge must not auto-update SkillUpForever, or it replaces the fork with the upstream release.
