# Tiderunner

A buoy-racing game for the browser, wrapped as an iOS and Android app with Capacitor.

Four boats, a winding river and a lot of red and green marks. The current runs with you —
keep the reds to port and stay off the sand.

The whole game is a single self-contained HTML file (`web/index.html`): canvas rendering,
physics, AI, procedural courses and Web Audio music, with no build step and no runtime
dependencies beyond a webfont.

## Racing

- **Eleven courses**, each with its own channel width and character, from wide meanders to
  technical hairpins. The menu leads with six — two full rows — and keeps the rest behind
  **More courses**;
  a course picked from the full list stays on show when the list collapses again.
  Generated meanders: Ouse Bends, Kingfisher Cut, Delta Run, Severn Bore, Reedham Weave and
  Atlantic Leg. Hand-drawn: Monaco Harbour, Amsterdam Cut, Fjord Run, Skerry Passage and
  Pool of London.
- **2, 3 or 5 laps** against three rivals (Sea Fret, Bramble and Mad Mackerel) at one of
  four skill tiers — Novice, Club, Regatta and Offshore — which scale rival power, braking
  discipline, apex commitment and how readily they go for boosts.
- **Momentum-based handling.** The hull carries its speed through a turn, so ease off before
  the mark and let the stern come round. Astern is available but slow.
- **Slipstream.** Sitting close behind another boat and roughly in line with it pulls you
  along.
- **Boost pickups** — surges of current placed off the ideal line, so taking one always
  costs a little cornering.
- **Run aground** and you lose way; stay stuck too long and a shark takes an interest, with
  a hard backstop a few seconds later. Getting eaten respawns you on the centreline.
- **Wildlife and scenery** — whales, rays, sharks and shoals of fish move through the
  channel, and moored yachts with cheering crews line the banks.

The HUD shows lap, position, elapsed time, last and best lap splits, speed in knots, and a
chartplotter mini-map of the course and the boats on it. Results are per-session; nothing
is written to disk.

## Controls

| Action | Keyboard | Touch |
| --- | --- | --- |
| Throttle | `W` / `↑` | **GO** pad |
| Astern | `S` / `↓` | **Reverse** pad |
| Helm | `A` `D` / `←` `→` | Left thumb joystick |
| Restart | `R` | — |

Touch controls appear automatically on coarse-pointer devices. The game honours
`prefers-reduced-motion` by dropping particle effects.

## Running it

There is no build step for the web version. Serve the `web/` directory and open it:

```bash
npx serve web
# or: python3 -m http.server -d web
```

Opening `web/index.html` directly from the filesystem also works. An internet connection is
only needed for the Google Fonts stylesheet — without it, the game falls back to system
fonts and plays normally.

## Building the apps

Capacitor is configured in `capacitor.config.json` (`com.foghorn.tiderunner`, web assets in
`web/`). The native projects are checked in under `android/` and `ios/`.

```bash
npm install
npx cap sync          # copy web/ into both native projects and update plugins
npx cap open android  # opens Android Studio
npx cap open ios      # opens Xcode
```

Re-run `npx cap sync` (or `npx cap copy`) after any change to `web/index.html`.

Targets: Android minSdk 24 / compileSdk 36, iOS 15.0+.

## Layout

```
web/index.html        the entire game — markup, styles, and all game code
capacitor.config.json app id, name and web asset directory
android/              Capacitor Android project
ios/                  Capacitor iOS project
```

Inside `web/index.html` the code is grouped into commented sections: course definitions and
track generation, boat physics and AI, audio, fauna, boosts, moored scenery, rendering, the
chartplotter, and the HUD and menu screens.

Courses are either generated — a closed loop with sinusoidal meanders laid over it — or laid
out by hand as control points. Hand-drawn courses have two limits to respect: no corner
tighter than the hull can turn, and no two reaches closer together than a channel width, or
they merge into one pool. Existing courses bottom out around a 65px corner radius and 1.6x
channel width of separation.
