# A&A Store — Dumpster & Trash Scavenging

Professional dumpster, trash bin, and scrap skip scavenging for FiveM. Players search containers that already exist on the map. The server rolls the loot, enforces the cooldown, and checks distance, time, and gloves before anything is given.

No custom props and no extra map. The resource uses vanilla GTA models.

| | |
|---|---|
| Resource name | `aastore_dumpster` |
| Version | 1.0.0 |
| Game | GTA V / FiveM |
| Lua | 5.4 (`cerulean`) |

## What players do

1. Walk up to a dumpster, street bin, or scrap skip.
2. Use the target eye, or press **E** when 3D text is shown.
3. Wait out the search animation and progress bar.
4. Receive the rolled items, or find nothing.
5. That exact container stays locked for everyone until its cooldown ends.

A search cancels if the player walks away, enters a vehicle, goes down, or presses **X** or **ESC**.

## Features

- Three container types and 37 vanilla props
- Separate label, icon, animation, search time, and cooldown per type
- Weighted loot with an empty chance and min / max amounts
- Cash paid as an item, or through the framework if the inventory rejects it
- Full-inventory warning. Items the player cannot carry are skipped
- Server-wide cooldown on the physical container, not just the player
- Glove protection. Cuts from glass or scrap without gloves, with a small chance of death at critical health
- Session token, distance checks, search-time check, and spam protection
- ox_target, qb-target, or 3D text plus **E**
- Built-in progress bar and notifications, or ox_lib / framework notifications
- English and Turkish locales

## Compatibility

| System | Supported |
|---|---|
| Framework | QBCore, Qbox, ESX, Standalone |
| Inventory | ox_inventory, qb-inventory, qs-inventory, ESX |
| Target | ox_target, qb-target, or none |
| Notifications | Framework, ox_lib, built-in HUD |
| Progress bar | Built-in HUD, ox_lib |

Framework, inventory, and target can be left on `auto`. The resource detects what is running.

## Requirements

- A FiveM server running artifact builds that support `cerulean` and Lua 5.4
- One of the frameworks above, or Standalone
- An inventory, if you want item rewards. Standalone can still pay cash through the framework bridge when one exists
- ox_lib only if `Config.Notify` or `Config.ProgressBar` is set to `ox_lib`
- ox_target or qb-target only if you do not want the 3D text fallback

`burger`, `bandage`, `lockpick`, and `cash` in the default loot table must already exist on your server. **Broken Glass** (`glass`) and **Work Gloves** (`eldiven`) are included and have to be added once. See [ITEMS.md](ITEMS.md).

## Installation

Full steps, server.cfg order, and item setup are in [INSTALLATION.md](INSTALLATION.md).

Short version:

1. Put the `aastore_dumpster` folder in your server `resources` directory.
2. Add the two items and copy their images. See [ITEMS.md](ITEMS.md).
3. Add this to `server.cfg`, after your framework and inventory:

```cfg
ensure aastore_dumpster
```

4. Restart the server, or run `ensure aastore_dumpster`.

## Configuration

Every option in `config.lua` is explained in [CONFIGURATION.md](CONFIGURATION.md): loot, cooldowns, containers, hazards, and locales.

These files are meant to be edited:

- `config.lua`
- `locales/en.json` and `locales/tr.json`
- `bridge/**/*.lua`
- `web/**/*`

## Links

- [Installation](INSTALLATION.md)
- [Configuration](CONFIGURATION.md)
- [Items and images](ITEMS.md)

A&A Store
