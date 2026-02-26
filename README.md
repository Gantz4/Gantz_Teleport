# 🚗 gantz_transportgi — Transport Job for FiveM

A fully-featured transport job resource for FiveM (QBox / QBCore / ESX), powered by **ox_lib**, **ox_target**, **oxmysql**, and **community_bridge**. Players pick up passengers, drive them to marked destinations, earn money and XP, and progress through a rank system.

---

## 📋 Features

- Immersive NPC at a fixed location — interact via ox_target
- Random passenger NPCs that enter/exit the vehicle with animations
- GPS blip + animated arrow marker for each destination
- Per-trip countdown timer (on-screen, no background box)
- XP & money rewards saved to MySQL after every trip
- 7-rank progression system with coloured menu cards
- Session HUD (top-right) showing live trips and earnings
- Trip-complete on-screen text with XP/money/rank gained
- End-of-shift summary menu with **live DB stats**
- Admin menu (`/admingi`) to create/delete delivery points in-game
- Full English & Spanish localisation — configurable in one line
- Compatible with **txAdmin**, **qbx_core**, **QBCore**, **ESX**

---

## ⚙️ Requirements

| Resource | Version | Notes |
|---|---|---|
| [ox_lib](https://github.com/overextended/ox_lib) | ≥ 3.x | Context menus, callbacks, progress bars, notifications |
| [ox_target](https://github.com/overextended/ox_target) | ≥ 3.x | NPC interaction |
| [oxmysql](https://github.com/overextended/oxmysql) | ≥ 2.x | Database persistence |
| [community_bridge](https://github.com/The-Order-Of-The-Sacred-Framework/community_bridge) | ≥ 0.13 | Framework detection, money, admin check |

---

## 🗄️ Database

The resource **automatically creates** the required table on every server start:

```sql
CREATE TABLE IF NOT EXISTS `gantz_transportgi_stats` (
    `identifier` varchar(60) NOT NULL,
    `xp`         int(11)     NOT NULL DEFAULT 0,
    `trips`      int(11)     NOT NULL DEFAULT 0,
    `earnings`   int(11)     NOT NULL DEFAULT 0,
    PRIMARY KEY (`identifier`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

> **Important:** If you created the table manually **without** a `PRIMARY KEY` on `identifier`, XP will not accumulate correctly. Drop and recreate it, or run:
> ```sql
> ALTER TABLE gantz_transportgi_stats ADD PRIMARY KEY (identifier);
> ```

---

## 📦 Installation

1. Copy the `gantz_transportgi` folder into your `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure gantz_transportgi
   ```
3. Make sure all dependencies are started **before** this resource.
4. The database table is created automatically on first start — no SQL import needed.

---

## ⚙️ Configuration (`config.lua`)

### Language

```lua
Config.Lang = 'en'   -- 'en' (English) or 'es' (Spanish)
```

Default is English. All UI text, notifications, and menus switch instantly.

---

### Main NPC

```lua
Config.MainNPC = {
    model  = 'a_m_y_business_02',
    coords = vector4(1388.4625, -747.1477, 67.1903, 74.3599)
    --                 X          Y          Z        Heading
}
```

The NPC is local-only (not networked), automatically ground-snapped, and frozen in place. Change `model` to any valid GTA5 ped model name. Change `coords` to wherever you want the NPC to stand.

---

### Vehicle

```lua
Config.Vehicle = {
    model  = 'baller',
    spawn  = vector4(1387.2421, -740.3605, 66.7123, 0.1694),
    jobTag = 'taxi'   -- used by vehicle key systems
}
```

`model` — any valid GTA5 vehicle model name.  
`spawn` — position and heading where the car spawns when the shift starts.  
`jobTag` — job name passed to vehiclelock/key systems (if used).

---

### Passenger NPCs

```lua
Config.Girls = {
    'a_f_y_topless_01',
    's_f_y_stripper_01',
    's_f_y_stripper_02',
    'a_f_y_juggalo_01'
}
```

Between 1 and 3 passengers spawn randomly per trip. Add or remove any GTA5 ped model names from this list.

---

### Rewards

```lua
Config.Rewards = {
    minMoney  = 500,    -- minimum cash per completed trip
    maxMoney  = 1500,   -- maximum cash per completed trip
    xpPerTrip = 100     -- XP earned per completed trip (fixed)
}
```

Cash is awarded randomly between `minMoney` and `maxMoney`. XP is always the fixed `xpPerTrip` value.

---

### Wait Time

```lua
Config.WaitTime = {
    min = 1,   -- minimum client wait time (minutes)
    max = 2    -- maximum client wait time (minutes)
}
```

After passengers exit the vehicle at the destination, a random countdown timer runs before they re-board. This simulates time with the client.

---

### Rank System

```lua
Config.Ranks = {
    { min = 0,     name = 'Novice',       icon = '🔘', color = '#aaaaaa' },
    { min = 500,   name = 'Driver',       icon = '🚗', color = '#44aaff' },
    { min = 1500,  name = 'Professional', icon = '⭐', color = '#44ff88' },
    { min = 3500,  name = 'Expert',       icon = '🌟', color = '#ffcc00' },
    { min = 7000,  name = 'Veteran',      icon = '💎', color = '#ff8800' },
    { min = 15000, name = 'Elite',        icon = '🏆', color = '#ff4444' },
    { min = 30000, name = 'Legend',       icon = '👑', color = '#cc44ff' },
}
```

Each rank requires `min` total XP. The player's current rank and next-rank progress are shown in the job menu and the end-of-shift summary.

- `min` — minimum accumulated XP to reach this rank (must start at `0` for the first entry)
- `name` — display name (shown in menu and trip-complete text)
- `icon` — emoji shown in the menu card title
- `color` — hex color used for the menu card accent (any CSS hex color)

You can add, remove, or reorder ranks freely. Always keep at least one entry with `min = 0`.

---

## 📍 Adding & Managing Delivery Points

Delivery points are saved to `locations.json` in the resource folder.

### In-game (recommended)

1. Grant yourself the permission (see [Admin Permissions](#-admin-permissions)).
2. Drive or walk to the location you want as a new delivery point.
3. Type `/admingi` in chat.
4. Select **Create new delivery point** — your current ground position is saved automatically with Z ground-snapped.
5. To remove a point, select **Delete delivery point** and choose from the list (coordinates shown in metadata).

### Manually (editing `locations.json`)

```json
[
  { "x": 1385.77, "y": -747.84, "z": 67.18 },
  { "x": -1200.0, "y": 300.0,   "z": 69.5  }
]
```

Add objects with `x`, `y`, `z` coordinates. The resource reloads the file each time it reads locations — no server restart required after saving this file while the resource is running.

> **Tip:** Use `/admingi` in-game for accurate ground Z values. Manual edits may cause markers to float if Z is wrong.

---

## 🔐 Admin Permissions

`/admingi` is accessible to any of the following:

| System | Ace / Method |
|---|---|
| txAdmin (any admin) | ace `command` |
| txAdmin group admin | ace `group.admin` |
| txAdmin group mod | ace `group.mod` |
| Custom ace | ace `gantz_transportgi.admin` |
| QBox / QB framework admin | `community_bridge:Framework().GetIsFrameworkAdmin` |
| Server console | always allowed |

To grant a non-txAdmin player access via custom ace, add to `server.cfg`:

```
add_ace identifier.license:XXXXXXXX gantz_transportgi.admin allow
```

---

## 🌐 Localisation

All user-facing strings are defined in `config.lua` under `Config.Locales`. Two languages are included by default: `en` and `es`.

To change language:
```lua
Config.Lang = 'en'   -- or 'es'
```

To add a new language, copy the `en` block, rename the key (e.g. `'fr'`), translate all strings, and set `Config.Lang = 'fr'`.

```lua
Config.Locales.fr = {
    target_label = "Gestion du Transport",
    menu_title   = "Gantz Transport",
    start_job    = "Commencer le travail",
    -- ... all other keys
}
```

---

## 📝 Commands

| Command | Permission | Description |
|---|---|---|
| `/admingi` | Admin only | Opens the admin menu to create or delete delivery points |

---

## 🎮 Player Flow

1. Find the NPC (purple blip on map — "Gantz Transport")
2. Interact via ox_target → menu opens with your rank card and stats
3. Click **Start Job** — a vehicle spawns, passengers board automatically
4. Follow the GPS blip to the destination (yellow arrow marker on ground)
5. Wait for the countdown timer, then passengers re-board
6. Trip completes — XP and money awarded, on-screen summary shown
7. Repeat until you click **Finish Job** — end-of-shift summary opens with live DB stats

---

## 🏗️ Resource Structure

```
gantz_transportgi/
├── fxmanifest.lua      Resource manifest
├── config.lua          All configuration (NPC, vehicle, rewards, ranks, locales)
├── client.lua          Client-side logic (NPC, menus, mission loop, render thread)
├── server.lua          Server-side logic (rewards, DB, admin commands, permissions)
├── locations.json      Saved delivery point coordinates
└── README.md           This file
```

---

## 📜 License

This resource is released for personal and server use. Redistribution or resale is not permitted without explicit permission from the author.
