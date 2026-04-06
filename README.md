# 🧟 Zombie Urban Survival
### A Terminal-Based Text Adventure Built with Python OOP

> *"The world has fallen. You wake up in a barricaded safehouse. The Radio Tower is your only way out."*

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![Genre](https://img.shields.io/badge/Genre-Survival%20Horror-darkred?style=flat-square)
![Type](https://img.shields.io/badge/Type-Text%20Adventure-gray?style=flat-square)

---

## 📖 Story

Civilization has collapsed. Zombies roam every street and alleyway of the city, and survivors are few. You wake up in a barricaded safehouse — your last safe shelter. A crackling radio signal hints at a possible extraction point: the **Radio Tower** on the north edge of the city.

To escape, you must loot weapons, survive enemy encounters, hunt down a randomly hidden **Keycard**, and fight your way through the Radio Tower — all the way to the rooftop where the **Mutated Alpha Boss** waits.

---

## 🚀 How to Run

```bash
python game.py
```

No external dependencies. Pure Python standard library.

---

## 🗺️ World Map & Location System

The world is split into **five major zones** connected by cardinal directions. What makes movement interesting is that each zone is not just a single room — every location has **multiple interior rooms** that you progress through one at a time.

```
                        [Radio Tower] 🔒 (Locked — needs Keycard)
                        ├─ Ground Floor       (Handgun, Bandage)
                        ├─ Stairwell          (Walker 🧟, Medkit)
                        ├─ Transmitter Room   (Runner ⚡, Shotgun, AK-47)
                        └─ Rooftop Control    (Mutated Alpha 💀, AR-15, Desert Eagle)
                               ↑ north
                        [Downtown Core]
                        ├─ Main Street        (entrance — Pipe, Crowbar)
                        ├─ Abandoned Mall     (Walker 🧟, Medkit)
                        └─ Dark Alley         (Walker 🧟, Revolver, Shotgun) ← deep room
               ↙ west          ↑              ↘ east
        [Overgrown Park]   [Downtown]    [Subway Tunnels]
        ├─ Park Entrance   (hub)         ├─ Station Platform   (entrance — Flashlight)
        ├─ Murky Pond                    ├─ Train Tracks       (Runner ⚡, Medkit)
        └─ Old Gazebo ← KEYCARD?         └─ Maintenance Room   (Walker 🧟, Mac-10, AK-47) ← KEYCARD?
```

### How Location Navigation Works

You are always in one of two states:

**At the Entrance (outside)**
```
> north              → travel to adjacent zone
> enter location     → step inside the first room
```

**Inside a Location (room by room)**
```
> go forward         → move deeper (next room)
> go backward        → retreat one room
> exit location      → leave entirely and return to the front
```

This room-by-room system means exploring each location is a deliberate decision with real risk — every step forward can put you face-to-face with a harder enemy, but also better loot.

---

## ⚔️ Enemies

Three enemy types with escalating threat levels. Each one has its own **infection chance** that adds a second health bar to worry about:

| Enemy | HP | STR | DEF | Infection Chance | Notes |
|---|---|---|---|---|---|
| 🧟 **Walker** (Minion) | 30 | 4 | 8 | 20% | Slow, easy to manage solo |
| ⚡ **Runner** (Elite) | 50 | 7 | 11 | 25% | Fast and hits hard — don't underestimate |
| 💀 **Mutated Alpha** (Boss) | 120 | 12 | 14 | 35% | Final boss. Drops the Radio on death |

When an enemy hits you, there's a percentage chance they also infect you. Infection stacks — and at **100% infection, you die instantly**, regardless of how much HP you have left.

### Ambush System
Enemies don't wait politely. When you enter or move deeper into a room containing an enemy, they **immediately ambush you** with randomised flavour text:

- *"💀 A Walker lurches out from the shadows!"*
- *"⚠️ You hear rapid footsteps... a Runner charges at you!"*
- *"🔥 The ground shakes... the Mutated Alpha has found you!"*

You cannot pick up items in a room until all enemies are cleared.

---

## 🎲 Combat System

Combat is **turn-based** and uses a **d20 dice roll** to determine hits — inspired by tabletop RPGs.

### Attack Resolution
```
Player rolls: d20 + STR vs Enemy DEF
If total > defense → hit (deals STR + weapon bonus damage)
If total ≤ defense → miss
```

### Player Combat Actions
| Command | Effect |
|---|---|
| `attack` / `a` | Roll d20 and attack |
| `use [item]` / `u [item]` | Consume a healing item mid-fight |
| `run` / `r` | 50% chance to escape combat |
| `inventory` / `i` | Check items without using a turn |

### Critical Hits
The player has a **20% critical hit chance** on every successful attack. A crit **doubles total damage**, including weapon bonus.

### Enemy Turn
Enemies roll `d20 + STR` against your defense. If they hit, they deal `1d4 + STR` damage — and may also apply infection (`1d3 + 5%` per successful hit, based on their infection chance).

---

## 🎒 Weapons & Items

### Weapon Arsenal (12 weapons)

Weapons are split by tier based on where they're found. **Firearms only appear in deep rooms** — you have to earn them.

| Tier | Weapons | Damage |
|---|---|---|
| 🔧 Melee (early game) | Rusty Bat, Tire Iron, Screwdriver, Pipe, Crowbar | 1–6 |
| 🔫 Light Firearms (mid/deep) | Handgun, Mac-10, Revolver | 5–9 |
| 🔫 Heavy Firearms (deep only) | Shotgun, AK-47, AR-15, Desert Eagle | 6–12 |

Damage is randomised within each weapon's range per attack (e.g. Shotgun rolls 8–12 each hit). Equipping a weapon adds its roll on top of your base STR damage.

### Consumables
| Item | HP Restored |
|---|---|
| Medkit | +30 HP |
| Bandage | +15 HP |
| Canned Food | +10 HP |

Consumables can be used **both in and out of combat**.

### Loot Tier System — Depth = Better Loot

Every location follows the same progression rule: melee weapons and basic consumables near the entrance, firearms and medkits only in the deepest rooms. Here's Downtown as an example:

| Room | Loot |
|---|---|
| Main Street (entrance) | Pipe, Crowbar, Bandage |
| Abandoned Mall (mid) | Canned Food, Medkit |
| Dark Alley (deep) | **Revolver, Shotgun**, Bandage, Medkit |

This forces you to push forward despite the risk, rather than looting safely from doorways.

---

## 🔑 The Keycard (Randomised Each Run)

The **Keycard** is required to unlock the Radio Tower. At the start of every game, it's **randomly placed** with a 50/50 chance in one of two deep locations:

- `Subway Tunnels — Maintenance Room`
- `Overgrown Park — Old Gazebo`

You won't know which one until you explore. This is the game's core replayability mechanic — it forces you to chart a different path each run.

---

## 🏆 Win & Lose Conditions

### ✅ Win — Step by Step
1. Explore locations and gear up
2. Find the **Keycard** (Subway Maintenance Room **or** Park Gazebo — random each game)
3. Travel north to the **Radio Tower** (Keycard unlocks the door)
4. Clear your way through 4 rooms: Ground Floor → Stairwell → Transmitter Room → Rooftop Control
5. Defeat the **Mutated Alpha** on the Rooftop
6. Pick up the **Radio** it drops
7. Type `use radio` at Rooftop Control
8. 🚁 Helicopter extraction — you escape!

### ❌ Lose Conditions
- Your **HP hits 0** in combat
- Your **infection level reaches 100%** (instant death, regardless of HP)

---

## 📋 Full Command Reference

### Navigation
| Command | Effect |
|---|---|
| `north` / `south` / `east` / `west` | Travel between major zones |
| `enter location` | Step inside current entrance |
| `go forward` | Move deeper into current location |
| `go backward` | Retreat one room |
| `exit location` | Leave entirely, return to entrance |
| `explore` | List adjacent zones |
| `look` | Re-describe current room |

### Items & Combat
| Command | Effect |
|---|---|
| `take [item]` / `grab` / `get` / `pickup` | Pick up an item (area must be clear) |
| `equip [weapon]` | Equip a weapon from inventory |
| `use [consumable]` | Use a healing item |
| `fight` / `attack` | Initiate combat |
| `inventory` / `i` | View inventory and equipped weapon |

### Other
| Command | Effect |
|---|---|
| `help` | Show command list |
| `quit` | Exit the game |

---

## 🧠 OOP Architecture

The game is built around a clean class hierarchy following the Single Responsibility Principle:

```
Character  (abstract base)
├── Player          — inventory, weapon equipping, crit system
└── Enemy
    ├── Minion      — Walker stats
    ├── Elite       — Runner stats
    └── Boss        — Mutated Alpha stats + Radio drop

Weapon              — name, damage range, per-attack roll
Location            — rooms, connections, enemies, items, lock state
Combat              — turn state machine (PLAYER_TURN → ENEMY_TURN → COMBAT_END)
Game                — main loop, state management, navigation orchestration
```

### Key Design Decisions
- **Inheritance over duplication** — `Player` and `Enemy` both inherit `attack()`, `take_damage()`, `heal()`, and `apply_infection()` from `Character`, with `Player` overriding `attack()` to add weapon bonus and crit logic
- **State machine for combat** — `Combat.PLAYER_TURN`, `ENEMY_TURN`, and `COMBAT_END` keep turn order clean and predictable
- **Two-mode navigation** — the `in_location` boolean and `active_location_entrance` pointer track whether you're inside a building or at its entrance, which controls which commands are available at any given moment
- **Depth-based loot** — items are assigned to `Location` objects at world creation time, with firearms explicitly placed only in the deepest rooms of each zone

---

## 💡 Strategy Tips

- **Don't rush north** — the Radio Tower will wreck you without guns. Hit the Subway and Park first
- **Firearms are always in the last room** — commit to clearing deep rooms to access them
- **Infection is the second clock** — even Walkers have a 20% infection chance per hit. Avoid unnecessary fights
- **Stock up before the tower** — the boss has 120 HP and hits for ~20 damage per turn
- **You can use items mid-combat** — `use medkit` during a fight is often the difference between surviving and dying
- **The run command is a last resort** — a 50% chance to fail, and a failed flee still gives the enemy a free attack

---

## 📁 Project Files

```
├── game.py        # Full game implementation (~600 lines)
├── design.md      # Game design document and class hierarchy planning
├── reflection.md  # OOP design reflection and lessons learned
└── README.md
```
