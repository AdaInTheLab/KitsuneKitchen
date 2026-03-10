# KitsuneKitchen — Hytale Data Pack Reference

Everything we learned building the KitsuneKitchen cooking mod, so we never have to figure it out twice.

---

## Data Pack Basics

### Structure
Data packs are **folders** (NOT zips) placed in `game/mods/`. The folder structure mirrors what's inside `Assets.zip`.

```
KitsuneKitchen/
├── manifest.json
├── Common/
│   └── Icons/
│       └── ItemsGenerated/
│           └── Food_Baked_Potato.png      (40x40 transparent PNGs)
├── Server/
│   ├── Languages/
│   │   └── en-US/
│   │       └── server.lang                (translation keys)
│   ├── Items/
│   │   └── Food_Baked_Potato.json         (item definitions)
│   ├── Recipes/
│   │   └── Recipe_Food_Baked_Potato.json  (recipe definitions)
│   └── Effects/
│       └── KK_Buff_Hearty.json            (buff/heal effects)
```

### manifest.json
```json
{
  "Group": "KitsuneDen",
  "Name": "KitsuneKitchen"
}
```

### Deploy Command
```bash
cp -r "C:/Users/darab/IdeaProjects/KitsuneKitchen/"* "F:/KitsuneDen/game/mods/KitsuneKitchen/"
```

---

## Recipes

### Campfire (Processing Bench)
- `"Type": "Processing"`, `"Id": "Campfire"`
- NO `Output` array — the output is implied
- NO Fuel in `Input`
- Simple input → output conversion

```json
{
  "Input": [
    { "ItemId": "Plant_Crop_Potato_Item", "Quantity": 1 }
  ],
  "BenchRequirement": [
    {
      "Type": "Processing",
      "Id": "Campfire"
    }
  ],
  "Output": [
    { "ItemId": "Food_Baked_Potato", "Quantity": 1 }
  ]
}
```

### Chef's Stove / Cooking Bench (Crafting Bench)
- `"Type": "Crafting"`, `"Id": "Cookingbench"`
- HAS `Output` array
- **MUST use `Categories` (plural, String array)** — NOT `Category` (singular string)
- Server silently ignores unknown keys and logs "Unused key" warnings

```json
{
  "Input": [
    { "ItemId": "Plant_Crop_Tomato_Item", "Quantity": 3 },
    { "ItemId": "Plant_Crop_Onion_Item", "Quantity": 1 },
    { "ItemId": "Ingredient_Spices", "Quantity": 1 }
  ],
  "BenchRequirement": [
    {
      "Type": "Crafting",
      "Id": "Cookingbench",
      "Categories": ["Prepared"]
    }
  ],
  "Output": [
    { "ItemId": "Food_Soup_Tomato", "Quantity": 1 }
  ]
}
```

### Category Values
- `"Categories": ["Prepared"]` — soups, stir fry, burgers, etc.
- `"Categories": ["Baked"]` — pies, baked goods

### ⚠️ GOTCHA: `Category` vs `Categories`
This was our biggest bug. Using `"Category": "Prepared"` (singular string) causes recipes to **silently not appear** at the bench. The server logs an "Unused key" warning but doesn't error. Always use `"Categories": ["Prepared"]` (plural, array of strings).

### KnowledgeRequired
Set `"KnowledgeRequired": true` on a recipe to make it show as `?` until the player learns it.

---

## Items

### Food Item JSON
```json
{
  "MaxStackSize": 16,
  "Rarity": "Uncommon",
  "Icon": "Icons/ItemsGenerated/Food_Soup_Tomato.png",
  "TranslationProperties": {
    "Name": "server.items.Food_Soup_Tomato.name",
    "Description": "server.items.Food_Soup_Tomato.description"
  },
  "Interactions": {
    "Root_Secondary_Consume_Food_T2": {}
  },
  "ConsumeCharge": {
    "StatusEffects": ["KK_Heal_T2", "KK_Buff_Hearty"],
    "StatusEffectTarget": "Self",
    "ConsumeInteraction": "Consume_Charge_Food_T2_Inner"
  }
}
```

### Consume Tiers
| Tier | Interaction Key | Consume Charge Key | Heal % |
|------|----------------|-------------------|--------|
| T1 | `Root_Secondary_Consume_Food_T1` | `Consume_Charge_Food_T1_Inner` | 5% |
| T2 | `Root_Secondary_Consume_Food_T2` | `Consume_Charge_Food_T2_Inner` | 10% |
| T3 | `Root_Secondary_Consume_Food_T3` | `Consume_Charge_Food_T3_Inner` | 15% |

### Rarity Values
- `"Common"` — basic items (T1 campfire foods)
- `"Uncommon"` — crafted foods (T2 bench recipes)
- `"Rare"` — advanced foods (T3 bench recipes)
- `"Epic"` — legendary items (Feast Pie)

---

## Effects & Buffs

### Instant Heal Effect
Duration is near-zero. Uses `StatModifiers` only.

```json
{
  "StatModifiers": {
    "Health": 0.05
  },
  "Duration": 0.1,
  "OverlapBehavior": "Overwrite"
}
```

| Effect | Health Value |
|--------|-------------|
| KK_Heal_T1 | 0.05 (5%) |
| KK_Heal_T2 | 0.10 (10%) |
| KK_Heal_T3 | 0.15 (15%) |

### Buff Effect (with stat boost + regen)
```json
{
  "RawStatModifiers": {
    "Health": [
      {
        "Amount": 1.10,
        "CalculationType": "Multiplicative",
        "Target": "Max"
      }
    ]
  },
  "StatModifiers": {
    "Health": 0.015
  },
  "OverlapBehavior": "Overwrite",
  "Duration": 900,
  "DamageCalculatorCooldown": 2,
  "StatusEffectIcon": "UI/StatusEffects/AddHealth/Small.png"
}
```

### Key Effect Fields
| Field | Purpose |
|-------|---------|
| `RawStatModifiers` | Stat boosts. Use `"Target": "Max"` + `"CalculationType": "Multiplicative"` for % increases |
| `StatModifiers` | Regen tick amount (Health or Stamina per tick) |
| `DamageCalculatorCooldown` | Tick rate in seconds. `2` = every 2s (health regen), `0.1` = very fast (stamina regen) |
| `Duration` | Buff length in seconds |
| `StatusEffectIcon` | Icon shown in the HUD buff bar |
| `OverlapBehavior` | `"Overwrite"` — re-eating replaces the buff |
| `DamageResistance` | Damage reduction. `"Physical"`, `"Projectile"`, etc. |

### ⚠️ GOTCHA: Missing `DamageCalculatorCooldown`
Without this field, buff icons won't show duration in the UI. Always include it.

### Status Effect Icons (from base game)
- `UI/StatusEffects/AddHealth/Small.png` — health (small)
- `UI/StatusEffects/AddHealth/Large.png` — health (large)
- `UI/StatusEffects/Stamina/Small.png` — stamina (small)
- `UI/StatusEffects/Stamina/Medium.png` — stamina (medium)

### Our Buff Lineup
| Buff | Duration | Max Stat Boost | Regen | Extra |
|------|----------|---------------|-------|-------|
| KK_Buff_Hearty (T2) | 900s (15min) | +10% Max Health | Health 0.015/2s | — |
| KK_Buff_Energized (T2) | 900s (15min) | +20% Max Stamina | Stamina 0.025/0.1s | — |
| KK_Buff_Spicy (T3) | 1800s (30min) | +30% Max Stamina | Stamina 0.04/0.1s | 5% Physical Resist |
| KK_Buff_Feast (T3) | 2700s (45min) | +20% HP, +15% Stam | Both 0.03/2s | 8% Phys+Proj Resist |

---

## Translation / Lang Files

### Location
```
Server/Languages/en-US/server.lang
```

### Format
```
items.Food_Baked_Potato.name = Baked Potato
items.Food_Baked_Potato.description = [TMP] Instantly restores <color is="#ffffff">5%</color> health.
```

Key format: `items.{ItemId}.name` and `items.{ItemId}.description`

### Referencing in Item JSON
```json
"TranslationProperties": {
  "Name": "server.items.Food_Baked_Potato.name",
  "Description": "server.items.Food_Baked_Potato.description"
}
```

Note: The JSON key is `server.items.X.name` (with `server.` prefix), but the lang file key is `items.X.name` (without prefix).

### ⚠️ GOTCHA: Literal strings don't work for Description
Putting a raw string in the `Description` field of `TranslationProperties` won't render — it just shows "Common Item". You MUST use translation keys that point to a `.lang` file.

---

## Rich Text in Lang Files

### ✅ What WORKS
```
<color is="#ffffff">text</color>     — colored text (note the is="" syntax!)
<b>text</b>                          — bold
<i>text</i>                          — italic
•                                    — bullet point (literal Unicode character U+2022)
\n                                   — newline
[TMP]                                — prefix used by base game
<item is="ItemId"/>                  — renders as clickable item name
```

### ❌ What DOES NOT WORK
```
<bullet>                             — doesn't exist, renders as broken text
<color=#ffffff>                      — Unity syntax, not supported
<color value="#ffffff">              — wrong attribute name
<span style="color:">               — HTML syntax, not supported
```

### ⚠️ GOTCHA: `<bullet>` broke everything
The `<bullet>` tag doesn't exist in Hytale. It was corrupting the text around it and making `<color>` tags look broken too. Use the literal `•` character instead.

### Color Palette (from base game)
| Hex | Color | Used For |
|-----|-------|----------|
| `#ffffff` | White | Values, stats, buff names, durations (most common) |
| `#4ade80` | Green | Numeric quantities, growth stages |
| `#ccaa00` | Gold | Warnings, WIP labels |
| `#be1717` | Red | Negative effects, durability loss |
| `#b61a82` | Purple | Special event names |

### Example Description (matching base game style)
```
items.Food_Soup_Tomato.description = [TMP] Instantly restores <color is="#ffffff">10%</color> health and grants:\n\n• <color is="#ffffff">Hearty</color> +10% Max Health, Health Regen\n\nDuration: <color is="#ffffff">15:00</color>
```

---

## Available Base Game Ingredients

### Crops
Plant_Crop_Potato_Item, Plant_Crop_Tomato_Item, Plant_Crop_Carrot_Item, Plant_Crop_Corn_Item, Plant_Crop_Onion_Item, Plant_Crop_Lettuce_Item, Plant_Crop_Chilli_Item, Plant_Crop_Aubergine_Item, Plant_Crop_Cauliflower_Item, Plant_Crop_Pumpkin_Item, Plant_Crop_Wheat_Item, Plant_Crop_Rice_Item, Plant_Crop_Turnip_Item

### Processed Ingredients
Ingredient_Dough, Ingredient_Flour, Ingredient_Salt, Ingredient_Spices

### Foods / Proteins
Food_Cheese, Food_Egg, Food_Beef_Raw, Food_Pork_Raw, Food_Chicken_Raw, Food_Wildmeat_Raw, Food_Bread

### Resource Types (for recipe inputs)
Fuel, Meats, Vegetables, Fruits, Mushrooms

---

## Icons

- **Size**: 40×40 pixels, transparent PNG
- **Location**: `Common/Icons/ItemsGenerated/{ItemId}.png`
- **Referenced in item JSON**: `"Icon": "Icons/ItemsGenerated/{ItemId}.png"`
- We made ours in Canva

---

## Our 12 Recipes at a Glance

### T1 — Campfire (Simple, no buffs)
| Item | Ingredients | Heal |
|------|------------|------|
| Baked Potato | 1 Potato | 5% |
| Grilled Corn | 1 Corn | 5% |
| Fried Egg | 1 Egg | 5% |

### T2 — Chef's Stove, Prepared (Buffed)
| Item | Ingredients | Heal | Buff |
|------|------------|------|------|
| Fries | 2 Potato, 1 Salt | 10% | — |
| Tomato Soup | 3 Tomato, 1 Onion, 1 Spices | 10% | Hearty 15min |
| Mushroom Soup | 3 Mushrooms, 1 Onion, 1 Spices | 10% | Hearty 15min |
| Veggie Stir Fry | 2 Vegetables, 1 Rice, 1 Spices | 10% | Energized 15min |
| Hamburger | 1 Beef Raw, 1 Bread, 1 Lettuce, 1 Tomato | 10% | Hearty 15min |
| Steak & Baked Potato | 1 Beef Raw, 2 Potato, 1 Salt, 1 Spices | 10% | Hearty 15min |

### T3 — Chef's Stove, Prepared/Baked (Strong buffs)
| Item | Ingredients | Heal | Buff |
|------|------------|------|------|
| Kimchi | 2 Lettuce, 1 Chilli, 1 Rice, 1 Spices, 1 Salt | 15% | Spicy 30min |
| Stuffed Aubergine | 2 Aubergine, 1 Rice, 1 Cheese, 1 Spices | 15% | Spicy 30min |
| Feast Pie | 1 Beef Raw, 1 Pork Raw, 2 Potato, 1 Carrot, 1 Onion, 1 Dough, 1 Spices | 15% | Feast 45min |

---

## Debugging Tips

1. **Recipe not showing at bench?** Check `Categories` is plural and an array: `["Prepared"]` not `"Prepared"`
2. **Buff icon missing?** Make sure the effect JSON has `DamageCalculatorCooldown` and `StatusEffectIcon`
3. **Tooltip says "Common Item"?** You need translation keys in a `.lang` file, not literal strings
4. **Tooltip text broken/garbled?** Check for invalid tags like `<bullet>`. Use `•` character instead. Use `<color is="#hex">` not `<color=#hex>`
5. **Server ignoring your changes?** The server logs "Unused key" warnings for unrecognized JSON fields — check the logs
6. **Item not stacking?** Check `MaxStackSize` in the item JSON
7. **Always restart the server** after deploying changes — there's no hot-reload for data packs
