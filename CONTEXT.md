# Queue Strain: context for a new session

Written 20 Nov 2026, to start a fresh chat. Read this first, then the code.

## What it is

An [oss!idle](https://oss.idle.rhythmgamers.net) add-on (4K only) by Rai. It shows where the queue you're playing takes
your strain/warm-up bar, XP per hour per skill, levels and prestige, and suggests better queues. Opened in game with
Alt+Shift+F. The README has the file map and the release steps.

## Status: paused

Since 1.4.08 (14 Nov) the in-game notice says Queue Strain is **paused**: no new features or fixes. Rai is rebuilding it
from scratch with a proper UI, as a new add-on. The only exception so far is **Suggestions → Get to Peak** (1.4.09–1.4.010).
Ask before adding anything else to this add-on.

## Two repos

| Repo | Role | Version |
| --- | --- | --- |
| `RaiLTC/queue-strain` | Public. oss!idle builds each version tag (`v1.4.013`). | 1.4.013 (18 Nov) |
| `RaiLTC/queue-strain-testing` | Testing builds. Work usually lands here first, then gets copied over. | 1.4.010 (18 Nov) |

The two have the same files (`src/`, `test/`, `scripts/`). Their version numbers don't line up: testing's v1.4.010 is
the same 7K fix as public 1.4.013.

Claude sessions could push to `queue-strain` (branch `claude/...`) but **not** to `queue-strain-testing`. There, push
access was blocked, so changes went over as patches that Rai applied (`git am`).

## Code map

- `src/config.js`: version, `VERSION_ID`, notice, changelog, hotfixes, settings, every player-facing text.
  A #.#.# release rewrites `CHANGELOG` and empties `HOTFIXES`; an x.x.x# release only adds to `HOTFIXES`.
- `src/rules/`: the game's fatigue, genetics, Tries, XP and route rules (ported from oss!idle 1.4.3.0).
  `fatigue.js` has `BAR_ZONES = [0.4, 0.6, 0.8]` and the stages: Cold / Warming Up below 0.4, Peak 0.4–0.6,
  Past Peak 0.6–0.8, Fatigued (the XP cut) from 0.8.
- `src/game.js`: reading the game and the server (queue, medals, genetics, Tries, playlists).
- `src/forecast.js`: simulating the queue play by play, the simulator rate limit, the caches (incl. the shared sim cache).
- `src/library.js`: the map library, top maps per skill plus a Most XP (all skills) list, saved in IndexedDB.
- `src/ui/window.js`: the window and every tab (Overview, XP, Speed Jam/Memory, Levels, Suggestions, Debug).
- `test/check.mjs`: `npm test` builds and checks the ported rules. `test/harness.html` is a fake game for a browser.

## What was done in the last long chat (12–13 Nov)

- Levels: routes from Gemini's level up sim. Only the next upgrade is labelled (`#4 2h 10m`), the next two show as
  faint lines, and bought upgrades are left out (issues #12–#14, closed).
- Suggestions (testing, 1.3.561 → 2de9673 in testing):
  - **Allow below Peak** toggle, next to Allow past Peak. Off keeps you from cooling out of Peak once reached.
  - A custom focus weight instead of a fixed ×2.
  - Most XP overall as a second kind of top performer, plus a Most XP (all skills) list in the Library.
- After that, in testing and then 1.4.0: Focus on was changed to max out the ticked skills (the weights were dropped),
  plus the shared sim cache, a library top 100, exact checks with Stop, Get to Peak and the 7K fix.

## Open issues (queue-strain)

- #6 Memory XP is bugged
- #5 Two-pass scan: stored answers first, then the simulator
- #3 New tab: map XP breakdown

## Working rules Rai likes

- Plain player-facing text, short changelog lines.
- Bump the version in `package.json`, `package-lock.json` and `src/config.js` together (the build checks they match).
- Run `npm run build` and `npm test` before committing.
