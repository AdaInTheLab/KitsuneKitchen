# 🍳 KitsuneKitchen

A cooking data pack for Hytale that adds 12 custom food items with unique buffs, crafted at the Campfire and Chef's Stove.

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)

---

## Overview

KitsuneKitchen expands Hytale's food system with a full progression of recipes — from simple campfire snacks to powerful feast dishes that grant combat buffs. Every item has a custom icon, styled tooltip, and meaningful gameplay effects.

## Food Items

### Tier 1 — Campfire

| Item | Ingredients | Heal |
|------|------------|------|
| Baked Potato | 1 Potato | 5% |
| Grilled Corn | 1 Corn | 5% |
| Fried Egg | 1 Egg | 5% |

### Tier 2 — Chef's Stove (Prepared)

| Item | Ingredients | Heal | Buff |
|------|------------|------|------|
| Fries | 2 Potato, 1 Salt | 10% | — |
| Tomato Soup | 3 Tomato, 1 Onion, 1 Spices | 10% | Hearty 15min |
| Mushroom Soup | 3 Mushrooms, 1 Onion, 1 Spices | 10% | Hearty 15min |
| Veggie Stir Fry | 2 Vegetables, 1 Rice, 1 Spices | 10% | Energized 15min |
| Hamburger | 1 Beef, 1 Bread, 1 Lettuce, 1 Tomato | 10% | Hearty 15min |
| Steak & Baked Potato | 1 Beef, 2 Potato, 1 Salt, 1 Spices | 10% | Hearty 15min |

### Tier 3 — Chef's Stove (Advanced)

| Item | Ingredients | Heal | Buff |
|------|------------|------|------|
| Kimchi | 2 Lettuce, 1 Chilli, 1 Rice, 1 Spices, 1 Salt | 15% | Spicy 30min |
| Stuffed Aubergine | 2 Aubergine, 1 Rice, 1 Cheese, 1 Spices | 15% | Spicy 30min |
| Feast Pie | 1 Beef, 1 Pork, 2 Potato, 1 Carrot, 1 Onion, 1 Dough, 1 Spices | 15% | Feast 45min |

## Buffs

| Buff | Effect | Duration |
|------|--------|----------|
| Hearty | +10% Max Health, Health Regen | 15 min |
| Energized | +20% Max Stamina, Stamina Regen | 15 min |
| Spicy | +30% Max Stamina, Stamina Regen, Physical Resistance | 30 min |
| Feast | +20% Max Health, +15% Max Stamina, All Regen, Damage Resistance | 45 min |

## Installation

1. Download or clone this repo
2. Copy the `KitsuneKitchen` folder to your server's `game/mods/` directory
3. Restart the server

```
game/
  mods/
    KitsuneKitchen/
      manifest.json
      Common/
      Server/
```

## Requirements

- Hytale Early Access server
- No other mods required — fully standalone

## Data Pack Structure

```
KitsuneKitchen/
  manifest.json
  Common/
    Icons/ItemsGenerated/         # 12 custom 40x40 food icons
  Server/
    Entity/Effects/Food/          # 7 effect files (4 buffs + 3 heals)
    Item/Items/Food/              # 12 food item definitions
    Languages/en-US/server.lang   # Tooltip translations with rich text
```

## License

[CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) — Free to share and remix, not for commercial use.

---

Made with love by [KitsuneDen](https://github.com/KitsuneDen)
