# 📍 Gantz Teleport — Dynamic Teleport System for FiveM

> **The most flexible and professional teleport solution for your FiveM server.**
> Create, manage, and use teleport points dynamically — no code editing required.

![FiveM](https://img.shields.io/badge/FiveM-Compatible-green?style=flat-square)
![QBox](https://img.shields.io/badge/QBox-✔-blue?style=flat-square)
![QB--Core](https://img.shields.io/badge/QB--Core-✔-blue?style=flat-square)
![ESX](https://img.shields.io/badge/ESX-✔-blue?style=flat-square)
![ox_lib](https://img.shields.io/badge/ox__lib-Required-orange?style=flat-square)
![Version](https://img.shields.io/badge/Version-2.0.0-brightgreen?style=flat-square)

🔗 **[📥 DOWNLOAD FOR FREE HERE TEBEX](https://gantz4-scripts.tebex.io/category/free-1)**  [![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/I3I3E12G5)

## ✨ Features

| Feature | Description |
|---|---|
| 🌐 **Multi-Language** | Full English & Spanish support with in-game language toggle — preference is saved per player |
| 🎯 **Dynamic Teleport Points** | Create unlimited teleport points anywhere on the map — no config editing needed |
| 🔵 **Visual Markers** | Beautiful blue ground markers with floating 3D text labels showing point names |
| 🚗 **Vehicle Support** | Teleports work seamlessly whether players are on foot or inside a vehicle |
| ⚙️ **Admin Panel** | Full CRUD management: create, rename, reposition, and delete points from an in-game menu |
| 🛡️ **Multi-Framework** | Auto-detects and works with **QBox**, **QB-Core**, and **ESX** out of the box |
| 🔐 **Flexible Permissions** | Supports ACE permissions, txAdmin admin roles, and framework group checks |
| 💾 **Database Storage** | All data persists in MySQL/MariaDB via oxmysql — survives restarts |
| ⏱️ **Anti-Spam Cooldown** | Configurable teleport cooldown prevents abuse |
| 🎨 **OX Lib Menus** | Clean, modern UI powered by ox_lib context menus |
| 🔊 **Sound Effects** | Optional teleport sound for immersive feedback |
| 📱 **Screen Effects** | Smooth fade-in/fade-out transition when teleporting |
| ☕ **Donate Button** | Built-in Ko-fi support link in the menu |

---

## 📸 How It Works

### For Players
1. **Walk into a blue marker** on the ground — you'll see the point name floating above it
2. A **help notification** appears: *"Press E for Teleport Menu"*
3. Press **E** to open the teleport menu
4. **Select a destination** from the list — you'll be teleported instantly with a smooth transition
5. If you're in a **vehicle**, it teleports with you
6. Use the **🌐 Language button** at the top to switch between English and Spanish

### For Administrators
1. Use the command **`/crearteleport`** or press **E** on any marker and select **⚙️ Administration**
2. **Create New Point**: Stand where you want the teleport and click create — enter a name and it's done
3. **Edit Existing Points**: View all points with coordinates, then:
   - 📍 **Update Position** — moves the point to your current location
   - ✏️ **Rename** — change the display name
   - 🗑️ **Delete** — permanently remove with confirmation dialog

---

## 📋 Requirements

| Dependency | Version | Link |
|---|---|---|
| **FiveM Server** | Latest recommended | [fivem.net](https://fivem.net) |
| **ox_lib** | v3.0.0+ | [GitHub](https://github.com/overextended/ox_lib) |
| **oxmysql** | v2.0.0+ | [GitHub](https://github.com/overextended/oxmysql) |
| **Framework** | QBox, QB-Core, or ESX | Any of the three |

---

## 🚀 Installation

### Step 1 — Download & Place
```
Place the `gantz_teleport` folder into your server's resources directory:
resources/[your_scripts]/gantz_teleport/
```

### Step 2 — Add to server.cfg
```cfg
# Make sure dependencies load first
ensure ox_lib
ensure oxmysql

# Then load gantz_teleport
ensure gantz_teleport

# Grant admin permissions (recommended)
add_ace group.admin gantz_teleport.admin allow
```

### Step 3 — Restart Server
The database table `gantz_teleports` will be created automatically on first start.

### Step 4 — Create Your First Teleport
1. Join the server as an admin
2. Go to the location where you want a teleport point
3. Type `/crearteleport` in chat
4. Click **➕ Create New Point**
5. Enter a name — done! A blue marker appears at your position

---

## ⚙️ Configuration

All settings are in **`config.lua`** — fully commented and easy to customize:

```lua
-- Default language ('en' or 'es')
Config.DefaultLanguage = 'es'

-- Marker appearance
Config.MarkerRadius = 2.0          -- Detection radius
Config.MarkerColor = { r=0, g=100, b=255, a=180 }  -- Blue RGBA
Config.MarkerScale = vector3(2.0, 2.0, 0.5)

-- Teleport cooldown (seconds)
Config.TeleportCooldown = 3

-- Admin permissions (any match = admin access)
Config.AdminAcePermissions = {
    'gantz_teleport.admin',  -- Custom ACE
    'command',               -- txAdmin default
}

-- Sound effects
Config.TeleportSound = true

-- Donate URL
Config.DonateURL = 'https://ko-fi.com/gantz4'
```

---

## 🔐 Admin Permissions

The script checks for admin access in this order:

| Priority | Method | Details |
|---|---|---|
| 1️⃣ | **ACE Permissions** | Checks all entries in `Config.AdminAcePermissions` — includes txAdmin's `command` ACE |
| 2️⃣ | **QBox Group** | Checks `PlayerData.group` via `qbx_core` export |
| 3️⃣ | **QB-Core Group** | Checks `PlayerData.group` via `qb-core` export |
| 4️⃣ | **ESX Group** | Checks `xPlayer.getGroup()` via `es_extended` |

Accepted groups: `admin`, `superadmin`, `god`

> **txAdmin users**: If you have admin in txAdmin, the `command` ACE is usually granted automatically — the script will detect it.

---

## 🌐 Language System

- Players can switch between **English** and **Spanish** via the 🌐 button in the menu
- Language preference is **saved per player** using FiveM's KVP system (persists across sessions)
- Default language is configurable in `config.lua`
- All UI text, notifications, and dialogs are fully translated

---

## 📁 File Structure

```
gantz_teleport/
├── fxmanifest.lua    # Resource manifest & dependencies
├── config.lua        # All settings, markers, permissions & translations
├── client.lua        # Markers, menus, teleport effects, language toggle
├── server.lua        # Database, admin checks, CRUD events, framework detection
└── README.md         # This file
```

---

## 🗄️ Database

The script automatically creates the following table:

```sql
CREATE TABLE gantz_teleports (
    id       INTEGER PRIMARY KEY AUTO_INCREMENT,
    name     VARCHAR(100) NOT NULL,
    pos_x    FLOAT NOT NULL,
    pos_y    FLOAT NOT NULL,
    pos_z    FLOAT NOT NULL,
    heading  FLOAT NOT NULL DEFAULT 0.0
);
```

---

## 🔧 Commands

| Command | Access | Description |
|---|---|---|
| `ES - /crearteleport US - /createteleport` | Admin only | Opens the admin management menu |

---

## ❓ FAQ

**Q: Does it work with QBox?**
A: Yes. The script auto-detects QBox (`qbx_core`) and uses its native API, with a fallback to the QB-Core bridge.

**Q: I'm admin in txAdmin but get "no permissions"**
A: Make sure `'command'` is listed in `Config.AdminAcePermissions` (it is by default). If using a custom setup, add `add_ace group.admin gantz_teleport.admin allow` to your `server.cfg`.

**Q: Can players create teleport points?**
A: No. Only admins can create, edit, and delete points. Players can only use existing destinations.

**Q: Does it teleport vehicles?**
A: Yes. If a player is inside a vehicle when they teleport, the entire vehicle (and player) moves to the destination.

**Q: Where is the data stored?**
A: In your MySQL/MariaDB database in the `gantz_teleports` table via oxmysql.

**Q: Can I add more languages?**
A: Yes! Add a new entry in `Config.Locales` in `config.lua` (e.g., `['fr']` for French) and update the toggle logic in `client.lua`.

---

## ☕ Support the Developer

If you enjoy this resource, consider supporting development:

**[☕ Buy me a coffee on Ko-fi](https://ko-fi.com/gantz4)**

---

## 📄 License

This resource is sold as-is. Redistribution or resale is prohibited.
© 2026 Gantz — All rights reserved.
## 📜 License

This resource is released for personal and server use. Redistribution or resale is not permitted without explicit permission from the author.
