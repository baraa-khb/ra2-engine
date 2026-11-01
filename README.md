# RA2 Engine

> A clean-room, open-source engine for **Command & Conquer: Red Alert 2 and
> Yuri's Revenge**. You bring the game files from your own copy; it runs the
> campaign, skirmish and multiplayer on **Windows, Android and iPhone/iPad**.

> **This is a fork of [ra2-engine](https://github.com/omarelarab/ra2-engine)
> by Omar El Arab**, who wrote the engine. The fork adds an **iPhone / iPad
> port built without a Mac**, **touch gestures** in place of the touch command
> column, and **crisp pixel fonts on phones and tablets**. All of it is
> offered upstream as pull requests.

**Status: playable beta (0.9).** Allied and Soviet campaigns, skirmish
against the AI, and LAN / Internet multiplayer for up to 8 players. The
engine contains no game data and never will: rules, art, sound, music and
movies come from the player's own legally obtained copy.

## Pick your lane

| You are... | Start at |
|---|---|
| A player who wants to install and play | [Part 1: For humans](#part-1-for-humans) |
| A developer who wants to build the code | [Part 1, "Build from source"](#build-from-source) |
| An AI agent, copilot or coding assistant | [Part 2: For LLMs](#part-2-for-llms-and-ai-agents) |

---

# Part 1: For humans

## What you need

- **Red Alert 2 with Yuri's Revenge**, installed from a legally owned copy
  (CD, The First Decade, Origin / EA App or Steam). The engine reads the
  Yuri's Revenge data, so a Red Alert 2-only install is not enough.
- Windows 10+, Android 5.0+, or an iPhone / iPad you can build for (below).

## The game files

Open your Red Alert 2 install folder (the one holding `ra2.mix`) and copy
**every `.mix` and `.csf` file**, about 1 GB total. That is the easy way to
be sure nothing is missing. The `.exe`, `.dll` and the rest are not used.

| File | Needed? | Used for |
|---|---|---|
| `ra2.mix`, `ra2md.mix`, `language.mix`, `multimd.mix` | **required** | rules, art, units, sounds, voices, menus, skirmish maps |
| `langmd.mix`, `ra2.csf` | recommended | Yuri's Revenge names and text |
| `theme.mix`, `thememd.mix` | recommended | music (both soundtracks) |
| `Maps01.mix`, `MAPS02.MIX`, `mapsmd03.mix` | recommended | campaign maps, default map |
| `expand01.mix`, `ecache01.mix`, ... | if present | official patch data |
| `MOVIES01.MIX`, `MOVMD03.MIX`, other `movies*` | if present | campaign cutscenes |
| `*.bik` | if present | cutscene videos (go in a `Movies/` folder on mobile) |

Only the four required files stop the game from starting. If something is
missing, the game opens a **GAME FILES NEEDED** page that lists what and
where it looked.

## Install

### Windows

1. Download the zip from the [Releases](../../releases) page and unzip it
   anywhere (or [build from source](#build-from-source)).
2. Put the game files next to `ra2game.exe`, or just start it: a **BROWSE**
   button on the GAME FILES NEEDED page picks your Red Alert 2 folder, and
   the choice is remembered. A standard retail install is also found through
   the registry, so on a PC that has the game it usually just starts.

### Android

1. Install the APK from the [Releases](../../releases) page.
2. Copy the game files to the phone (for example `Download/RA2`) and open
   **RA2 Engine**. Tap **Import game files**, pick the folder, done.

### iPhone and iPad (experimental, this fork)

No Mac, no Xcode, no App Store: the port builds with
[xtool](https://xtool.sh) on **Linux, macOS or Windows (WSL)** and installs
on the device with your own Apple ID (a free one works). Then import the
game files in the app through the iOS Files picker.

```sh
git clone https://github.com/baraa-khb/ra2-engine && cd ra2-engine
ios/scripts/build.sh          # asks everything it needs, then builds
```

Run it with no arguments and it walks you through the whole setup: xtool,
the SDK from your Xcode `.xip` download, login, your Red Alert 2 folder, and
installing on the connected device. The full story, including what goes
where on the device and the current limitations, is in
**[ios/README.md](ios/README.md)**.

## Multiplayer

- **LAN:** games on the same network show up automatically; on iOS, join by
  address or use the lobby server (see the limitations in ios/README.md).
- **Internet:** a small lobby / relay server you host yourself, one binary,
  no accounts: **[docs/SERVER.md](docs/SERVER.md)**.
- Up to 8 players, AI slots, the original game types, alliances, chat and
  beacons. Everyone needs the same engine version and the same game files;
  the lobby checks both.

The complete player guide (controls, movies, saves, troubleshooting) is
**[docs/INSTALL.md](docs/INSTALL.md)**.

## Build from source

Desktop (CMake 3.20+, Ninja, a C++17 compiler; Python 3 for the tools;
SDL2 2.30.9 is fetched automatically):

```sh
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo
cmake --build build
build/ra2game         # the game (ra2engine is the console build)
build/ra2assets_tests # the unit tests
```

Android APK (JDK 17+, Android SDK and NDK r27c; configure the desktop build
first, or point `RA2_SDL2_SRC_DIR` at an SDL 2.30.9 source folder):

```sh
cd android && ./gradlew assembleDebug
```

iPhone and iPad: see [above](#iphone-and-ipad-experimental-this-fork), or
one command for a personal build that bundles your own game files and
installs on the device:

```sh
ios/scripts/build.sh --game-data /path/to/your/RA2 --install
```

Developer reference, test and determinism gates, and the architecture
notes: **[docs/DEVELOPMENT.md](docs/DEVELOPMENT.md)**.

## Known limitations

- In this fork the campaign mission picker (Single Player > New Campaign)
  is hidden while it is being reworked; saved campaigns still open.
- Yuri's faction is not playable (Allied and Soviet only).
- Some details still differ from the original: units tilting on slopes, the
  Siege Chopper deploy, the Spy Plane, the random map generator.
- Phone-versus-PC multiplayer is off by default until it is tested more.
- Saved games do not carry over between engine versions yet.

---

# Part 2: For LLMs and AI agents

You are reading this because a model was pointed at this repository. This
section is written for you, and everything in it is a hard requirement, not
a suggestion.

## What this repository is

A from-scratch C++17 / SDL2 engine that runs the original Red Alert 2 and
Yuri's Revenge campaigns, skirmish and multiplayer using the player's own
game files. It ships **no game data**: no `.mix` archives, art, audio, maps
or rules. Behaviour is calibrated against the original game; the findings
live in `docs/re/` in our own words. Licence: 0BSD, see LICENSE and
THIRD_PARTY_NOTICES.md.

## Repository map

| Path | What lives there |
|---|---|
| `engine/` | the game engine: `.cpp` units plus `.inc` include-units (platform code in `platform*.cpp`, networking in `mp/`, touch input in `touch_*`) |
| `src/` | ra2assets: the asset pipeline that turns player files into engine data |
| `tests/` | 1134 test cases in 114 files (doctest), from unit to real-play lockstep |
| `android/` | Gradle project, JNI bridge, touch UI |
| `ios/` | xtool build scripts, Info.plist, setup guide (ios/README.md) |
| `docs/` | INSTALL.md (player guide), SERVER.md (lobby server), DEVELOPMENT.md (developer reference), `re/` (original-behaviour notes) |
| `tools/`, `cmake/` | build tooling; `cmake/sdl2_pin.cmake` pins SDL2 2.30.9 everywhere |

## Build and test (verify before you claim anything works)

```sh
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo && cmake --build build
build/ra2assets_tests                     # all tests
build/ra2assets_tests --test-case="net*"  # one module (fast)
```

Android: `cd android && ./gradlew assembleDebug`. iOS:
`ios/scripts/build.sh` (no arguments runs the guided setup; see
ios/README.md). CI-grade gates (determinism, golden bytes, the `--play`
harness) are described in docs/DEVELOPMENT.md; read it before touching
`engine/`.

## Hard rules

1. **Never commit game data.** `*.mix`, `*.csf`, `*.bik`, `*.aud` and
   friends are Electronic Arts assets; `.gitignore` blocks them on purpose.
   A Xcode `.xip` is each user's personal Apple download; it never enters
   the repository either.
2. **Do not "fix" the ignored folders.** `ios/local/`, `ios/generated/`,
   `ios/Vendor/`, `build/` are machine-local or personal by design.
3. **New platform behaviour goes behind guards.** iOS code is `RA2_IOS`
   (defined only by Package.swift); behaviour shared with Android is
   `defined(__ANDROID__) || defined(RA2_IOS)`. Desktop paths must stay
   untouched.
4. **Match the original game, never copy from it.** Findings in `docs/re/`
   are our own words; EA code, assets and text never enter this repo.
5. **Tests are part of done.** Engine changes ship with cases in `tests/`;
   run the suite before you say a change works.
6. **Dev servers bind to 127.0.0.1.** The LAN discovery broadcast is game
   networking, not an excuse for a wildcard bind anywhere else.
7. **Plain text only in code, docs and commits:** ASCII, straight quotes,
   no em dashes, no emoji.

## Where to go next

- `docs/DEVELOPMENT.md` before changing `engine/`
- `docs/re/<topic>.md` for how the original game behaves in an area
- `ios/README.md` for the iOS port, `android/` for the Android app
- Git log for the W-numbered work-stream convention used in the docs

---

## Legal

This is a non-commercial fan project. It contains an engine only and **does
not include or distribute any Command & Conquer: Red Alert 2 / Yuri's
Revenge game data**, no art, sounds, music, movies, maps or rules files.
The engine runs only on game files that the player supplies from their own
legally obtained copy.

"Command & Conquer", "Red Alert" and "Yuri's Revenge" are trademarks of
Electronic Arts Inc. This project is not affiliated with, endorsed by or
sponsored by Electronic Arts.

## Licence

Zero-Clause BSD (0BSD). The full text is in [LICENSE](LICENSE); third-party
components keep their own licences, listed in
[THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

Engine author: **Omar El Arab** ([omarelarab/ra2-engine](https://github.com/omarelarab/ra2-engine)).
Fork (iOS port, touch gestures, mobile fonts): Baraa Al Khateeb.
