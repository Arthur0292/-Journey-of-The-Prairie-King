# Journey of the Prairie King — RISC-V Assembly Demake

A full recreation of the "Journey of the Prairie King" arcade mini-game from *Stardew Valley*, implemented in RISC-V Assembly and developed as a project for the Introduction to Computer Systems course at the University of Brasília.

![Assembly](https://img.shields.io/badge/Assembly-RISC--V-blue)
![RARS](https://img.shields.io/badge/Simulator-RARS-orange)
![Status](https://img.shields.io/badge/status-completed-success)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## Overview

In *Stardew Valley* (ConcernedApe, 2016), players can visit the Stardrop Saloon and play arcade machines, one of which is "Journey of the Prairie King": a top-down shooter in which the player must survive waves of enemies, collect power-ups, purchase upgrades, and defeat a boss to progress through each level.

This repository contains a complete demake of that mini-game, implemented from scratch in RISC-V Assembly and executed in the RARS simulator. The project reproduces the core mechanics of the original game, including movement, directional shooting, collision detection, enemy pursuit AI, a heads-up display, MIDI-based music, and win/loss conditions.

## Features

- Three playable maps (desert, snow, and grass), converted from PNG images into byte matrices rendered through the RARS Bitmap Display
- Grid-based player movement (`W A S D`), with collision checks performed before each step
- Directional shooting system (`I J K L`), including sound effects and automatic projectile removal on impact
- Collision detection based on a binary map (0 = free, 1 = obstacle) generated from the scene's sprite data
- Enemy pursuit AI that moves toward the player's position every frame
- Dynamic HUD displaying remaining lives, updated whenever the player takes damage
- Background music implemented through RARS MIDI calls, with note data extracted via HookTheory
- Win and loss conditions: defeating the required enemies across all three levels, or losing all lives

## Technology Stack

| Technology | Purpose |
|---|---|
| RISC-V Assembly | Core implementation language |
| RARS | Simulator/IDE used for execution, Bitmap Display, and MIDI |
| Bitmap Display (RARS) | Rendering of scenes and sprites |
| MIDI (RARS) | Playback of menu, victory, and game-over soundtracks |

## Repository Structure

```
.
├── funcoes/                    # Core game logic
│   ├── apagar.s
│   ├── apagar_tiro.s
│   ├── apagar_vida.s
│   ├── colisao.s
│   ├── colisao_inimigo.s
│   ├── colisao_tiro.s
│   ├── desenhar_inimigos.s
│   ├── desenhar_vida.s
│   ├── mover_inimigo.s
│   ├── print.s
│   ├── print_imagem.s
│   ├── print_tiro.s
│   ├── spawnar_inimigos.s
│   └── tirar_inimigos.s
├── sprites/                     # Scene, character, and HUD sprite data
│   ├── HUD/                     # Heart/life indicator sprites
│   ├── fase_2/                  # Stage 2 sprite set (snow)
│   ├── fase_3/                  # Stage 3 sprite set (grass)
│   ├── cenario1.s / cenario2.s  # Stage backgrounds
│   ├── frente.s / costas.s / esquerda.s / direita.s   # Player sprites
│   ├── inimigo_frente.s / inimigo_costas.s / inimigo_esquerda.s / inimigo_direita.s  # Enemy sprites
│   ├── mapa_colisao1.s / mapa_colisao2.s / mapa_colisao3.s  # Per-stage collision maps
│   ├── menu.s / game_over.s / game_win.s
│   └── tiro.s                   # Projectile sprite
├── main.s                       # Program entry point
├── Rars16_Custom1.jar           # RARS simulator build used for this project
└── Documentação Projeto ISC.pdf # Full project report
```

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

## Technical Highlights

- **Image-to-matrix conversion**: scenes (320x240) were converted from PNG images into byte vectors representing each pixel, allowing them to be loaded directly through the RARS Bitmap Display.
- **Dedicated collision maps**: each level has its own binary matrix, checked before any movement (player, projectile, or enemy) to allow or block displacement.
- **Careful memory management in Assembly**: register and memory layout were organized to keep the program stable and lightweight, one of the most significant challenges of the project.

## Documentation

The full project report, including methodology and results, is available in `Documentação Projeto ISC.pdf`.

## License

This project is distributed under the MIT License. See the `LICENSE` file for details.
