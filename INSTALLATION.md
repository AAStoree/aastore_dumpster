# Installation

This guide assumes the resource folder is named `aastore_dumpster`. Do not rename it unless you also change the `ensure` line.

## 1. Add the resource

Copy the `aastore_dumpster` folder into your server `resources` directory. A subfolder such as `resources/[aastore]/aastore_dumpster` is fine.

The folder must contain `fxmanifest.lua` at its root.

## 2. Start order

Start the framework and inventory before this resource. ox_lib and your target script should also be started first when you use them.

```cfg
ensure ox_lib
ensure ox_inventory
ensure ox_target
ensure aastore_dumpster
```

Use the resources you actually run. Examples:

**QBCore + qb-inventory + qb-target**

```cfg
ensure qb-core
ensure qb-inventory
ensure qb-target
ensure aastore_dumpster
```

**Qbox**

```cfg
ensure qbx_core
ensure ox_inventory
ensure ox_target
ensure aastore_dumpster
```

**ESX Legacy**

```cfg
ensure es_extended
ensure ox_inventory
ensure ox_target
ensure aastore_dumpster
```

If you use the default ESX inventory instead of ox_inventory, skip `ensure ox_inventory` and set `Config.Inventory = 'esx_inventory'`.

## 3. Add the new items

The default loot uses `burger`, `cash`, `bandage`, and `lockpick`. Those are common framework items. Confirm they exist before going live.

Two items ship with this resource and are not part of a default framework:

| Item name | Label | Purpose |
|---|---|---|
| `glass` | Broken Glass | Loot item |
| `eldiven` | Work Gloves | Blocks scavenging injuries while it is in the inventory |

Images are in `install/images/`:

- `eldiven.png`
- `glass.png`

Paste the item definitions and copy both images using the guide for your inventory: [ITEMS.md](ITEMS.md).

## 4. Configure

Open `config.lua`. Leaving the detectors on `auto` is enough for a normal server:

```lua
Config.Framework = 'auto'
Config.Inventory = 'auto'
Config.Target = 'auto'
```

Set `Config.Locale` to `'en'` or `'tr'`.

Loot, cooldowns, and container models are covered in [CONFIGURATION.md](CONFIGURATION.md).

## 5. Restart

Restart the server, or in the server console:

```
ensure aastore_dumpster
```

After a config change:

```
restart aastore_dumpster
```

## 6. Confirm it works

1. Join the server and walk up to a street dumpster.
2. You should see a target option, or `[E] Search Dumpster`.
3. Finish a search. You either receive items or the empty-container message.
4. Search the same dumpster again. It should refuse until the cooldown ends.
5. Put `eldiven` in your inventory and search again. You should not take cut damage.

Turn `Config.Debug = true` if a search does nothing. Matching lines are printed in the server console as `[AAStore:Dumpster:DEBUG]`. Set it back to `false` on a live server.

## Common problems

**No prompt on the dumpster**

- The resource is not started. Check the console for errors on `ensure`.
- `Config.Target` is `auto`, but neither ox_target nor qb-target is running, and you are too far away for the text fallback. Interaction distance is `Config.MaxDistance` (2.8 by default).
- You are in a vehicle or downed. Both block the prompt.

**Search starts, then nothing is given**

- The empty roll succeeded. Default `emptyChance` is 52.
- The item name in `Config.Loot` does not exist in your inventory. The add fails quietly except for the full-inventory message.
- The inventory was full.

**"This container was recently searched"**

- That world position is still on cooldown. Default per-container cooldown is 600 seconds. This is shared by every player.

**Gloves do not protect**

- The item name in the inventory must be one of: `eldiven`, `gloves`, `glove`, `scavenge_gloves`, `work_gloves`.
- The item has to be in the inventory at the moment the search completes. Wearing it as clothing is not checked.

**ox_lib progress bar or notify does nothing**

- `ox_lib` is not started, or it is started after this resource.
- `Config.Notify` / `Config.ProgressBar` is not set to `ox_lib`.

**ESX item insert fails**

- Your `items` table columns differ from ESX Legacy. Adjust `install/esx_inventory.sql` to match your table, then run it again.
