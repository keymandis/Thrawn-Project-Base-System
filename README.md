# Thrawn Project — Base Systems Prototype Documentation

> **Status:** Pre-Alpha — Active Development  
> **Context:** Assets and systems are currently decentralized across multiple developers(models, scripts, ui's). A unified developer environment is not yet available. This document serves as the authoritative reference for all planned and in-progress systems so incoming team members understand implementation status before a centralized build exists.

---

## Table of Contents

0. [Changelog](#0-changelog)
1. [How to Read This Document](#1-how-to-read-this-document)
2. [Base Game Systems](#2-base-game-systems)
3. [Custom Systems](#3-custom-systems)
4. [Systems At a Glance](#4-overview)
5. [Open Decisions](#5-open-decisions)

---
 
## 0. Changelog
 
All notable updates to this document are recorded here. Most recent first.
 
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-03-13 | keymandis | Initial document. Base systems, custom systems, open decisions, systems documented. |
| 0.2 | 2026-03-13| keymandis | Added section 0 for documenting changelogs for base system. |

---

## 1. How to Read This Document

Each system is marked with a readiness indicator:

| Tag | Meaning |
|-----|---------|
| `(Y)` | Coding assets fully prepared. Ready to import. |
| `(H)` | Assets exist but are not yet fitted to the Thrawn Project. Above 70% complete or needs reconfiguration. |
| `(N)` | Not yet developed, in early prototype, or pending purchase from an external developer. |

Systems without a tag are small in scope and can be delivered in a matter of hours once the environment is centralized.

Some systems will be **commissioned or purchased** from external developers to reduce delivery time. This will be discussed on a per-system basis.

---

## 2. Base Game Systems

All systems in this project run through a custom **ModuleLoader framework**. Every service and module follows the same lifecycle: registered at boot, initialized synchronously via `:Init()`, then started concurrently via `:Start()`. Scripting contributors are expected to be familiar with this pattern before working on any system below.

These are the foundational systems the game cannot run without. All other systems depend on or integrate with these.

---

### PlayerCharacterConfigs `(H)`

Controls how the player character looks and behaves physically.

- **Rig type:** R6 or R15 — TBD (see [Open Decisions](#6-open-decisions))
- **Head rotation** — optional config
- **Ragdoll on death** `(Y)` — asset ready

---

### PlayerData System `(H)`

Handles all persistent player data. Loads on join, saves on leave.

Stores:
- Last time joined
- XP and level
- Rank
- Any additional fields added per system

> Asset exists but the data template is not yet configured to the Thrawn Project schema. Needs to be fitted once the rank and progression structure is finalized.

---

### GroupData Module `(Y)`

Utility module that links Roblox group data to gameplay. Used by the rank/team structure handler and any system that gates logic behind group rank.

Ready to import.

---

### Inventory / Item System `(H)`

Base inventory system exists. The **inventory type is not yet decided.** Options being considered:

| Type | Description | Reference |
|------|-------------|-----------|
| Fixed Slot | Basic custom inventory, functionally similar to Roblox default | — |
| Grid-Based (spatial, weight, container) | Full spatial grid with weight limits and containers | BRM5 |
| Radial Wheel + Stack | Radial selection with item stacking | The Last of Us |
| Weapon Wheel Only | Weapon-only radial wheel | GTA V |

> Decision required before this system can be fully configured. See [Open Decisions](#5-open-decisions).

---

### Console / Command System `(H)`

In-game admin and command interface. Options:

- **Premade** (e.g. HD Admin) — faster setup, limited flexibility
- **Custom** — preferred for this project

Custom implementation includes:
- Rank system integration
- Custom commands, easily extendable
- Type bar with configurable prefix (e.g. `\`)
- Searchable console UI — click to execute without typing

---

### Main Menu `(H)`

Two approaches being considered:

- **Main menu based** — players land on a menu screen before entering the game
- **Direct game entry** — players spawn directly into the world

> TBD. See [Open Decisions](#5-open-decisions).

---

### Supporting Utilities

The following smaller systems are ready or near-ready and require minimal setup time:

| System | Tag | Notes |
|--------|-----|-------|
| Error handling / debug modules | `(Y)` | |
| FPS / ping statistics display | `(Y)` | |
| Character sprint system | `(Y)` | |
| Character morph loader | `(Y)` | 3D or classic clothing — TBD |
| Request throttlers | `(Y)` | |
| Anti-cheat validator helpers | `(Y)` | |
| Map object handlers | `(Y)` | Doors, boxes, interactables |
| Map loader | `(Y)` | |

---

## 3. Custom Systems

These systems build on top of the base layer and the ModuleLoader framework. Each one is a standalone service that integrates with others through the loader — no direct coupling between systems. These define the game's core gameplay identity.

---

### Rank / Team Structure Handler `(H)`

The central authority system. Governs team hierarchy, permissions, and command structure.

Sub-systems:
- **PlayerData integration** — rank stored and loaded per player `(H)`
- **Promotion system** — handles rank changes `(H)`
- **Discord bot integration** — slash command linkage (planned, TBD)
- **Announcement system** — in-game broadcast by authority
- **Assignment system** — commanders assign tasks to units
- **Markers** — positional markers visible by rank/team
- **Authority handler** — gates who can issue orders, orbital strikes, etc.
- **Hologram handlers** `(N)` — holographic briefing/display system
- **Formation system** `(N)` — structured unit formations, or brute-forced by commander orders (TBD)

---

### Gun System `(H)`

Server-authoritative firearm system. Exploit-proof by design.

| Feature | Tag |
|---------|-----|
| Exploit-proof validation | `(Y)` |
| Projectile handlers — custom or FastCast | `(Y)` |
| Bullet handlers / laser beam support | `(Y)` |
| Custom per-gun configs (fire rate, bullet type, etc.) | `(H)` |
| Extra features: wall-banging, ricochet, armor validation | `(H)` |

---

### Cooldowns Module `(Y)`

Shared utility module. Sets debounce intervals for any action across any system. Ready to import and use immediately.

Usage pattern:
```lua
local Cooldowns = cooldowns.new("cooldown" .. player.UserId)
if cooldowns:IsReady("cooldown" .. player.UserId) then
    coldowns:Set("cooldown" .. player.UserId, 0.1)
    -- handlers
end
```

---

### Map Systems `(H)`

| Feature | Tag | Notes |
|---------|-----|-------|
| Objective modules | `(N)` | Checkpoint captures, sabotage, linked to rank/assignment system |
| Environmental destruction | `(H)` | Buildings, terrain. Shovel item TBD |

---

### STC (Space Traffic Control) System `(N)`

Coordinates air and ground traffic. Links to:
- Ground command
- Air command
- Ground units
- Air units

Needs to be developed from scratch.

---

### Flight System `(H)`

> Greenlit acquired

Planned features:
- Multiple flight configs: gravity-based (traditional), gravity-defying (TIE fighters), custom physics profiles
- Optimized laser/firearm handler
- Aircraft-specific implementation
- **Handling mode TBD:** Arcade (semi-realistic) or Physics-based (stalling, engine management, realistic handling)

---

### Ground Vehicle System `(H)`

Two delivery paths being considered:

| Path | Delivery Time |
|------|--------------|
| Purchase premade system | Under 1 day (setup + configs) |
| Custom from scratch(not completely) | 4–5 days |

---

### Physiology System

Two tiers available. Which tier is used is an open decision.

#### Tier 1 — Roblox Default HP `(Y)`
Standard health system. Fast to ship. No custom behavior.

#### Tier 2 — Custom Medical / Physiology System `(H)`

Highly detailed, server-authoritative medical simulation. Fully configurable gore/realism level:

| Level | Description |
|-------|-------------|
| Semi-Realistic | Ragdoll, bullet hit SFX, blood spatter particles |
| Fully Realistic | Body behaves as close to real-life as technically feasible |
| Hollywood Realistic | Dramatic film-style — most Roblox games use this tier |

**Core features:**
- Server-authoritative blood tracker (BloodLoss, BloodLevel, Blood Pressure, Cardiac Output)
- Hemorrhage calculators and symptom applier
- Hit area handler
- Infection handling `(H)`

**Physiology effects (sample — not exhaustive):**
- Low blood
- Trauma response
- Femur fracture — movement disabled
- Unconsciousness

**Trauma types:**
| Type | Tag |
|------|-----|
| Penetration Trauma | `(H)` |
| Blunt Force Trauma | `(N)` |
| Blast / Explosive Trauma | `(N)` |

**Custom HumanoidType support** — different species can have different physiology configs:
- `HomoSapiens` (human) `(Y)`
- Extensible — new types addable in minutes

> Full MedicalConfigs are highly detailed and are documented separately. Request via DM if needed.

---

## 4. overview

| System | Tag | Needs Decision |
|--------|-----|----------------|
| PlayerCharacterConfigs | `(H)` | Rig type |
| PlayerData System | `(H)` | Data scheme |
| GroupData Module | `(Y)` | — |
| Inventory / Item System | `(H)` | Inventory type |
| Console / Command System | `(H)` | Premade vs custom |
| Main Menu | `(H)` | Menu vs direct entry |
| Error Handling | `(Y)` | — |
| FPS / Ping Display | `(Y)` | — |
| Sprint System | `(Y)` | — |
| Morph Loader | `(Y)` | Clothing type |
| Request Throttlers | `(Y)` | — |
| Anti-Cheat Helpers | `(Y)` | — |
| Map Object Handlers | `(Y)` | — |
| Map Loader | `(Y)` | — |
| Rank / Team Structure | `(H)` | Discord integration scope |
| Gun System | `(H)` | — |
| Cooldowns Module | `(Y)` | — |
| Map Objectives | `(N)` | — |
| Environmental Destruction | `(H)` | — |
| STC System | `(N)` | — |
| Flight System | `(N)` | Arcade vs physics |
| Ground Vehicle System | `(H)` | Purchase vs custom |
| Physiology System | `(H)` | Tier 1 vs Tier 2 |

---

## 5. Open Decisions

These decisions are blocking or will significantly affect implementation. They need to be resolved before the relevant systems can be fully configured and presented from centralized assets.

| # | Decision | Affects |
|---|----------|---------|
| 1 | R6 or R15 rig? | PlayerCharacterConfigs, animations, physiology |
| 2 | Inventory type? (Fixed / Grid / Radial / Weapon wheel) | Inventory system, item system, UI |
| 3 | Main menu or direct entry? | Main menu, map loader |
| 4 | Console — premade or custom? | Console/command system, rank integration |
| 5 | Flight handling — arcade or physics-based? | Flight system purchase spec |
| 6 | Ground vehicles — purchase or build? | Vehicle system, timeline |
| 7 | Physiology — default HP or custom medical? | Physiology, gun system, UI |
| 8 | Formation system — built or commander-ordered? | Rank/team structure handler |
| 9 | Discord bot integration scope? | Rank handler, announcement system |
| 10 | Centralized dev environment — when? | All systems, asset pipeline |

---

*Document maintained by keymandis, supervised by TyrantLevel(Mike). Update as decisions are made and systems progress.*