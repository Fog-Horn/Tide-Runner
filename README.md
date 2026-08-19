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
  **More courses**; a course picked from the full list stays on show when the list
  collapses again.
  Generated meanders: Ouse Bends, Kingfisher Cut, Delta Run, Severn Bore, Reedham Weave and
  Atlantic Leg. Hand-drawn: Monaco Harbour, Amsterdam Cut, Fjord Run, Skerry Passage and
  Pool of London.
- **2, 3 or 5 laps** against three rivals (Sea Fret, Bramble and Mad Mackerel) at one of
  four skill tiers — Easy, Normal, Hard and Insane. The tiers are mostly driving skill
  rather than horsepower: they scale how much of the theoretical corner speed a rival will
  use, how much of the river it takes to straighten a bend, how steady it holds its line,
  how fast it moves the helm, and whether it checks for clear water before grabbing a boost.
  From Normal upward they also race each other — tucking into the wake ahead for the tow,
  then pulling out to pass once there's clear water and the speed to use it.

  Roughly what each tier laps Ouse Bends in: Easy ~35s, Normal ~28s, Hard ~24s, Insane ~22s.
  For scale, the Insane driving line on the player's own engine laps it in ~23.5s — so
  beating Insane means driving a near-perfect line *and* using the boosts and slipstream
  it leaves on the table.
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

The HUD keeps the instruments in one row along the top — lap, position, speed in knots and
elapsed time with last lap, session best and course record — leaving the chartplotter the
top-right corner and the bottom of the screen clear for the touch controls.

**Course records** are saved per course and survive a reload, shown on the menu tile and in
the HUD while you race. They live in `localStorage` under `tiderunner.records.v1`; storage
that refuses to answer (private windows, sandboxed frames) is handled, and records simply
stop persisting beyond the session rather than breaking the game.

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
