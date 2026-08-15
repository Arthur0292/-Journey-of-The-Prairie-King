# Journey of the Prairie King — RISC-V Assembly Demake

A full recreation of the "Journey of the Prairie King" arcade mini-game from *Stardew Valley*, implemented in RISC-V Assembly and developed as a project for the Introduction to Computer Systems course at the University of Brasília.

![Assembly](https://img.shields.io/badge/Assembly-RISC--V-blue)
![RARS](https://img.shields.io/badge/Simulator-RARS-orange)
![Status](https://img.shields.io/badge/status-completed-success)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## Overview

In *Stardew Valley* (ConcernedApe, 2016), players can visit the Stardrop Saloon and play arcade machines, one of which is "Journey of the Prairie King": a top-down shooter in which the player must survive waves of enemies, collect power-ups, purchase upgrades, and defeat a boss to progress through each level.

This repository contains a complete demake of that mini-game, implemented from scratch in RISC-V Assembly and executed in the RARS simulator. The engine drives its own game loop, enemy AI, collision system, sprite double-buffering, HUD, and MIDI-based soundtrack directly through memory-mapped I/O.

## Features

- Three distinct levels (desert, snow, and grass), each with its own background, sprite set, and collision map
- Grid-based player movement (`W A S D`) with per-axis collision checks against the level's obstacle map
- Directional shooting system (`I J K L`), with sound effects on fire and automatic despawning on wall or enemy impact
- Enemy spawn system that activates up to four enemies at a time on a fixed timer
- Enemy pursuit AI that steps toward the player along whichever axis (x or y) has the larger distance, blocked by the same collision map used by the player
- Player damage and temporary invincibility frames on contact with an enemy
- Dynamic HUD showing remaining lives via a heart icon and numeric sprite, refreshed on every hit
- Progression system requiring a fixed number of kills per level to advance, culminating in a win screen
- Full menu, victory, and game-over screens, each with a looping MIDI soundtrack

## Technology Stack

| Technology | Purpose |
|---|---|
| RISC-V Assembly | Core implementation language |
| RARS | Simulator/IDE used for execution, Bitmap Display, keyboard input, and MIDI |
| Bitmap Display (RARS) | Rendering of scenes and sprites via memory-mapped pixel buffers |
| MIDI (RARS) | Playback of menu, victory, and game-over soundtracks |

## Repository Structure

```
.
├── funcoes/                    # Core game logic
│   ├── apagar.s                 # Erase a sprite from the display
│   ├── apagar_tiro.s            # Erase a projectile from the display
│   ├── apagar_vida.s            # Erase HUD life sprite
│   ├── colisao.s                # Player/enemy vs. level collision map check
│   ├── colisao_inimigo.s        # Player vs. enemy hitbox collision and damage
│   ├── colisao_tiro.s           # Projectile vs. enemy collision (kill logic)
│   ├── desenhar_inimigos.s      # Redraw active enemies each frame
│   ├── desenhar_vida.s          # Redraw HUD life sprite
│   ├── mover_inimigo.s          # Enemy pursuit AI
│   ├── print.s                  # Generic sprite blit to the bitmap display
│   ├── print_imagem.s           # Full background image blit
│   ├── print_tiro.s             # Projectile sprite blit
│   ├── spawnar_inimigos.s       # Enemy spawn scheduler
│   └── tirar_inimigos.s         # Deactivate/clear all enemies (level transitions)
├── sprites/                     # Scene, character, and HUD sprite data
│   ├── HUD/                     # Heart and life-count sprites
│   ├── fase_2/                  # Stage 2 sprite set (snow)
│   ├── fase_3/                  # Stage 3 sprite set (grass)
│   ├── cenario1.s / cenario2.s  # Stage backgrounds
│   ├── frente.s / costas.s / esquerda.s / direita.s   # Player sprites
│   ├── inimigo_frente.s / inimigo_costas.s / inimigo_esquerda.s / inimigo_direita.s  # Enemy sprites
│   ├── mapa_colisao1.s / mapa_colisao2.s / mapa_colisao3.s  # Per-stage collision maps
│   ├── menu.s / game_over.s / game_win.s
│   └── tiro.s                   # Projectile sprite
├── main.s                       # Program entry point, game loop, and state machine
├── Rars16_Custom1.jar           # RARS simulator build used for this project
└── Documentação Projeto ISC.pdf # Full project report
```
## Game maps

<img width="316" height="239" alt="image" src="https://github.com/user-attachments/assets/2b88d54f-f43b-45a1-86c3-d70a9391cbb2" />

<img width="299" height="233" alt="image" src="https://github.com/user-attachments/assets/e6c77af3-ccff-42b8-b61d-69e63c94ae00" />

<img width="308" height="236" alt="image" src="https://github.com/user-attachments/assets/e4f8d0de-cef4-416d-93ea-c8a12ae51a1b" />

## Getting Started

1. Download and install [RARS](https://github.com/TheThirdOne/rars) (`.jar`, requires Java), or use the `Rars16_Custom1.jar` build included in this repository.
2. Clone this repository:
   ```bash
   git clone https://github.com/Arthur0292/Journey_of_The_Prairie_King.git
   ```
3. Open RARS and load `main.s`.
4. Enable the Bitmap Display and MIDI tools from the RARS Tools menu, configuring the display dimensions to match the scene resolution (320x240).
5. Assemble and run the program.

## Controls

| Key | Action |
|---|---|
| `W` | Move up |
| `A` | Move left |
| `S` | Move down |
| `D` | Move right |
| `I` | Shoot up |
| `J` | Shoot left |
| `K` | Shoot down |
| `L` | Shoot right |
| `N` | Skip to the next level (debug) |

## Implementation Details

**Rendering and double buffering.** Sprites are written to one of two frame buffers, selected by shifting a frame index (0 or 1) into the display's base address before computing the pixel offset (`row * 320 + col`). The main loop alternates between buffers every iteration and erases each entity's previous position before redrawing it at its new one, avoiding full-screen redraws.

**Collision system.** Each level owns a 320x240 binary collision map, where `1` marks an obstacle and `0` marks free space. Before the player, an enemy, or a projectile moves, the target position is checked in 4-pixel steps against the map; a hit blocks the movement outright.

**Enemy AI.** Every few frames (governed by `MOVE_INTERVAL`), each active enemy compares its position to the player's and steps two pixels along whichever axis has the greater distance, again validated against the collision map. The enemy's facing sprite is derived from the direction it last moved.

**Damage and invincibility.** Enemy contact reduces the player's life by one and sets a fixed invincibility window (`PLAYER_INVENCIVEL`), which is decremented every frame and blocks further damage until it expires.

**Level progression.** Each level tracks its own kill counter; reaching a fixed kill threshold triggers a transition that resets enemy state, loads the next level's collision map, sprite set, and background color, and clears the spawn timer.

**Audio.** Menu, victory, and game-over music are stored as flat arrays of `(pitch, duration)` pairs and played back note-by-note through the RARS MIDI syscall, looping automatically once the track ends.

## Documentation

The full project report, including methodology and results, is available in `Documentação Projeto ISC.pdf`.

## License

This project is distributed under the MIT License. See the `LICENSE` file for details.
