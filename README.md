# mac3

**GTA III, native on Apple Silicon.** A build that renders through
**Metal**, runs sharp at full **Retina** resolution, and turns on macOS **Game
Mode** automatically. One command turns your own copy of the original game —
Steam, retail disc, or files from an old PC — into a ready-to-play
`Grand Theft Auto III.app`. No administrator password needed.

This repository hosts the **download only** — there is no source code here.

> ⚠️ **This needs the original GTA III, not the Definitive Edition.** The
> Definitive Edition is a different, rebuilt game and will not work.

## Install

**You need:** an Apple Silicon Mac (M1 or newer) on macOS 26 (Tahoe) or later, and
your own copy of the **original GTA III** — the classic 2001/2002 PC
release, *not* the Definitive Edition
([Steam](https://store.steampowered.com/app/12100/) works out of the box — you
never have to launch it).

1. Open **Terminal** (`Cmd+Space`, type `Terminal`, press Enter), paste this
   line and press Enter:

   ```sh
   curl -fsSL https://raw.githubusercontent.com/gtamac/mac3/main/quick-install.sh | bash
   ```

   It finds your game files automatically (a Steam copy, or an already-installed
   app from an earlier run — including the previous mac3.app or re3.app) and
   puts **Grand Theft Auto III.app** in your Downloads folder — about a minute,
   the app is ~1.2 GB with the game inside.

2. When Finder opens, **drag Grand Theft Auto III into Applications** — or just
   double-click it to play right away.

**Using a non-Steam copy?** Add the path to the end of the command — a game
folder (the one with `models`, `data`, `audio`, …) or a GTA III `.app` that
contains the files (including Wineskin/Wine and CrossOver wrappers, such as the
`GTA 3.app` on a mounted "Grand Theft Auto 3" disc image) both work — again,
from the original game, not the Definitive Edition:

```sh
curl -fsSL https://raw.githubusercontent.com/gtamac/mac3/main/quick-install.sh | bash -s -- ~/Downloads/"Grand Theft Auto 3.app"
```

**Upgrading?** Run the same one-liner again — it reuses the game files from your
installed app.

Prefer a classic disk image? Add `--dmg` to get a drag-to-Applications
`Grand Theft Auto III.dmg` instead: `... | bash -s -- --dmg`.

## Direct downloads

| File | What it is |
|---|---|
| [`mac3-macos-arm64.tar.gz`](https://raw.githubusercontent.com/gtamac/mac3/main/mac3-macos-arm64.tar.gz) | the app with no game assets, for building your own bundle |
| [`mac3-macos-arm64.zip`](https://raw.githubusercontent.com/gtamac/mac3/main/mac3-macos-arm64.zip) | the same, zipped |
| [`quick-install.sh`](https://raw.githubusercontent.com/gtamac/mac3/main/quick-install.sh) | the installer the one-liner runs |
| [`signtool-arm64.tar.gz`](https://raw.githubusercontent.com/gtamac/mac3/main/signtool-arm64.tar.gz) | the bundled ad-hoc signer, so installing needs no Xcode |

## Legal

mac3 requires the files from your own legally purchased copy of the original
Grand Theft Auto III. No game assets are distributed here.

## What's new

**Latest update**

- **Fixed a rare crash with MetalFX upscaling on**: an object removed at
  exactly the wrong moment mid-frame could take down the motion-vector
  pass. Those objects are now dropped from the pass safely.
- **Vegetation casts translucent ray-traced shadows**: trees, bushes and
  fences throw soft shadows that let part of the light through, instead
  of solid silhouettes or nothing. And the map's painted-on shadows — the
  fixed dark patches baked into the ground under trees, docks, the
  airport and the el-train track — disappear while ray tracing is on,
  replaced by the real traced shadows, and come back the moment it's off.
- **mac3 now needs macOS 26 (Tahoe) or later.** The whole bundle is now
  built against the macOS 26 MetalFX runtime.
- **The FPS Graphics option** replaces the old frame-limiter toggle: plain
  **30** (the default) or **No Limit** (uncapped, breaks physics). MetalFX
  frame interpolation was ported too, but ships disabled — it is still too
  buggy on III.
- **Per-object motion vectors**: the temporal scaler no longer sees only
  camera motion. A velocity pass re-renders the moving objects — vehicles,
  pedestrians, props — into a motion-vector buffer shared with MetalFX, so
  dynamic motion is reconstructed correctly, not smeared. Each vehicle is
  treated as one rigid body (so wheels and doors don't tear off).
- **Ray tracing lights up the night**: every car headlight now throws a
  halogen low beam onto the road, starting softly just ahead of the bumper.
  Headlights follow the game's own switching logic — cars light up one by
  one through dusk, and a midday storm fills the street with lit beams —
  and their tint matches the car: near-white halogens on the sports and
  luxury cars, warm ~3000K beams on ordinary traffic, dim yellow sealed
  beams on the old clunkers. Held weapons now cast ray-traced shadows too.
  Muzzle flashes now sit at the very end of the barrel and scale with the
  weapon, from a pistol's pop to an M16's blaze, and no two shots flare
  alike. Rockets in
  flight trail a flickering flame that lights the street as they pass, and
  the police helicopter's searchlight is now a true volumetric cone — a
  godray beam hanging in the air that casts a crisp pool on the ground, and
  anything caught in it carves dark shafts through the beam.
- **The rocket launcher's sight no longer shows as a solid black square**
  with MetalFX upscaling on — additive HUD draws now composite correctly.
- **MetalFX upscaling is all-temporal now** (the spatial scaler is retired):
  presets run best to off — **Best Quality** (100% — pure temporal
  anti-aliasing, no upscale), **Quality** (85%), **Balanced** (75%, the
  default), **Performance** (67%), **Max Performance** (50%), **Ultra
  Performance** (33%), and off for native rendering.

## The port

GTA III running natively on Apple Silicon, with the same treatment
[macVC](https://github.com/gtamac/macVC) gives Vice City.

**Rendering**

- Renders through Metal (via ANGLE) instead of Apple's deprecated OpenGL
- HDR: true extended-range highlights on XDR and HDR displays, on by default
  (Graphics → HDR)
- MetalFX upscaling, all through the temporal scaler — presets from Best
  Quality (100%, pure anti-aliasing) through Balanced (75%, the default) down
  to Ultra Performance (33%), or Off for native resolution. Applied from the
  main menu, before you load a save.
- Metal ray tracing (experimental, off by default): ray-traced sun shadows
  with god rays by day; street lamps, per-era halogen headlights, muzzle
  flashes, explosions, rocket flames and the police searchlight's
  volumetric cone light the night for real
- 4x MSAA by default, plus mipmapped, trilinear and anisotropically filtered
  textures
- Extended draw distance with the original fog, and vehicles that fade in with
  distance instead of popping into view
- All three islands stay loaded, so there is no pause crossing the bridges

**macOS**

- Opens in native fullscreen on the display you launched it from, at your
  screen's real Retina resolution
- Game Mode turns on automatically while it runs
- Settings and saves live in `~/Library/Application Support/mac3/`, safely
  outside the app
- Fixes the classic "mouse sometimes not detected" bug, and keeps the cursor
  inside the game in fullscreen menus
- Tells you when a new version is out, and can install it for you

**Installing**

- One command builds a ready-to-play `Grand Theft Auto III.app` from your own
  copy of the game
- Finds the Steam copy on its own, including the radio stations and mission
  dialogue that the Steam release stores away from the game files
- Needs no administrator password and no Xcode
