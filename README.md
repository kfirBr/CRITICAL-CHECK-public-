# Critical Check ♟️

A **roguelike tactical chess battler** with **real-time online multiplayer**. Chess pieces keep their classic movement patterns, but instead of instant capture they have **HP, armor, and classes** — so every battle is a small tactics puzzle. Build an army across short, escalating runs (à la *Balatro*), draft upgrades between fights, and take your build online against other players across **mobile and PC**.

Built **solo** in **Godot 4 / GDScript**, with a custom **Node.js** multiplayer backend deployed on **AWS**.

> _Developed with the help of Claude Code (AI pair programming)._

<!-- Optional: add a wide hero GIF or banner here — the board mid-battle looks best. -->
<!-- ![Critical Check](docs/hero.gif) -->

---

## See it in action

<!--
  Record a short clip for each (5-15s), convert to .gif (e.g. ezgif.com or ffmpeg),
  and save them in a docs/ folder as: docs/battle.gif, docs/shop.gif, docs/online.gif
  GitHub autoplays and loops GIFs right here. Keep each under ~5-10 MB.
-->

**A battle**

![A battle in Critical Check](docs/battle.gif)

**Drafting upgrades between fights**

![Drafting upgrades in the shop](docs/shop.gif)

**Online multiplayer — phone vs PC**

![An online multiplayer match](docs/online.gif)

---

## Download & Play

No setup or account needed — grab the latest build and play:

| Platform | Download |
|---|---|
| **Windows** | **[Download (.zip)]((https://github.com/kfirBr/CRITICAL-CHECK-public-/blob/main/CRITICALCHECK.zip))** — unzip and run `CRITICAL CHECK.exe` |
| **Android** | **[Download (.apk)]((https://github.com/kfirBr/CRITICAL-CHECK-public-/blob/main/CRITICAL%20CHECK.apk))** — open the file and allow the install |

**Online multiplayer works out of the box** — the game connects to its live server automatically, so just pick a point bracket and you'll be matched with another player, phone or PC.

---

## Highlights

- **~17,000 lines of GDScript across 48 modules** — combat, enemy AI, procedural encounters, save system, and all UI written from scratch.
- **Real-time online 1-v-1** with a **custom snapshot-relay netcode** over WebSockets — full **cross-play between Android and PC** builds.
- **Node.js backend** for matchmaking and a **persistent global leaderboard**, deployed on **AWS Lightsail** (Ubuntu) behind a **Caddy** reverse proxy for TLS (`wss`), kept alive with **PM2**.
- **Seven playable classes** (Warrior, Mage, Paladin, Necromancer, Void, Engineer, King), each with a unique passive and class-specific consumable abilities.
- **Headless balance simulator** that auto-plays full runs with the real game logic to tune difficulty and the in-game economy.

---

## Gameplay

- **Chess movement, RPG combat.** Pieces move like their chess counterparts but attack at range and have HP/attack/armor. Capturing is a fight, not an instant kill.
- **Run structure.** Place your army, win a battle, then spend points in a shop to buy new pieces and draft upgrades before the next, harder fight.
- **Multiple modes.** Standard runs, an Endless mode, a 5-area **Roadmap** campaign, local hot-seat **Versus**, and **online Versus**.
- **Depth.** Pawn promotion and double-first-step (real chess rules), stackable defensive **Guard** upgrades, status effects (poison / burn / freeze), mines and walls, and **permadeath** in solo runs — dead pieces leave the run.

---

## Architecture

The game is structured around a small set of global singletons and a clean battle scene graph.

**Core singletons (autoloads):**

| System | Responsibility |
|---|---|
| `GameManager` | Scene transitions, global game state, run modes |
| `RunManager` | Run data — pieces, upgrades, economy, combat hook dispatch |
| `NetManager` | Online multiplayer networking over WebSockets |
| `JuiceManager` | Screen shake, hit-stop, CRT post-processing |
| `SoundManager` | Procedurally generated SFX + real-time synthesized music |

**Battle scene** is split into focused nodes — `GridManager` (board state), `TurnManager` (a PLAYER↔ENEMY phase state machine), `CombatManager` (damage resolution + signals), `AIController` (paced enemy turns), and pure-visual `Board` / VFX layers. Chess movement lives in a single static `MovementCalculator`. All piece and UI art is **drawn in code** via `_draw()` — there are no sprite sheets.

---

## Online Multiplayer

The most technically involved part of the project.

- **Matchmaking:** players pick a point bracket; the Node.js server pairs the next two players in the same bracket and assigns seats.
- **Build exchange:** each device runs class-select → shop → placement locally, then serializes its full build and sends it to the opponent.
- **Battle sync — snapshot relay:** only the *acting* device computes moves, attacks, and abilities. On "End Turn" it ships the entire resolved board state (every piece's stats, status effects, ability flags, and ability charges) to the opponent, who replays a visual action log and rebuilds the board. Because just one side ever computes a turn, **there is nothing to desync**.
- **Cross-platform + secure:** a Caddy reverse proxy terminates TLS so Android clients can connect over `wss://`, enabling phone-vs-PC matches.

---

## Tech Stack

- **Engine / language:** Godot 4, GDScript (typed)
- **Backend:** Node.js (`ws` WebSocket library), HTTP JSON API, JSON persistence
- **Cloud / DevOps:** AWS Lightsail (Ubuntu), Caddy (TLS reverse proxy), PM2, Git
- **Platforms:** Windows (desktop) and Android (APK), cross-play

---

## About

Solo project by **Kfir Brandt** — designed, programmed, and deployed end-to-end, from gameplay logic to live cloud backend. Built with the assistance of **Claude Code** as an AI pair-programmer.

*The source code is kept in a private repository; this page showcases the finished game. Happy to walk through the code on request.*
