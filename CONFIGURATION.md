# Configuration

All gameplay settings live in `config.lua`. After any edit, run:

```
restart aastore_dumpster
```

## General

```lua
Config.Locale = 'en' -- en, tr
Config.Debug = false
```

| Option | Values | What it does |
|---|---|---|
| `Config.Locale` | `en`, `tr` | Language file loaded from `locales/`. Unknown languages fall back to English. |
| `Config.Debug` | `true`, `false` | Prints search, distance, cooldown, and exploit notes to the server console. |

## Framework, inventory, and target

```lua
Config.Framework = 'auto'   -- auto, qbcore, qbox, esx, standalone
Config.Inventory = 'auto'   -- auto, ox_inventory, qb_inventory, qs_inventory, esx_inventory
Config.Target = 'auto'      -- auto, ox_target, qb_target, none
```

`auto` picks the first match that is started:

- Framework: Qbox (`qbx_core`), then QBCore (`qb-core`), then ESX (`es_extended`), otherwise Standalone
- Inventory: ox_inventory, then qs-inventory, then qb-inventory, then the framework default
- Target: ox_target, then qb-target, otherwise `none`

`Config.Target = 'none'` disables the target eye and uses 3D text plus **E**. The text loop sleeps while the player is far from a container.

## Notifications, progress, and sound

```lua
Config.Notify = 'framework' -- framework, ox_lib, ui
Config.ProgressBar = 'ui'   -- ui, ox_lib
Config.EnableSound = true
```

| Option | Values | What it does |
|---|---|---|
| `Config.Notify` | `framework` | QBCore, Qbox, or ESX notification. Standalone uses a basic GTA feed notification. |
| | `ox_lib` | ox_lib notify. ox_lib must be started. |
| | `ui` | Built-in card in the top of the screen, with sound when `EnableSound` is true. |
| `Config.ProgressBar` | `ui` | Built-in bar with a percentage. |
| | `ox_lib` | ox_lib progress bar. Movement, vehicles, and combat are disabled by that bar. |
| `Config.EnableSound` | `true`, `false` | UI notification chimes. Has no effect when notifications are not `ui`. |

## Distance and cooldowns

```lua
Config.MaxDistance = 2.8
Config.DefaultCooldown = 300 -- seconds
Config.PlayerActionCooldown = 3 -- seconds between searches
```

| Option | Unit | What it does |
|---|---|---|
| `Config.MaxDistance` | meters | How close the player must be to start, and to stay, during a search. The server allows a small extra margin for latency. |
| `Config.DefaultCooldown` | seconds | Used when a container type has no `cooldown` of its own. |
| `Config.PlayerActionCooldown` | seconds | Minimum wait before the same player can start another search. Stops key spam. |

Container cooldowns are shared. When one player finishes a search, that world position is locked for every player. The lock is based on the container coordinates, so a restarted client does not reset it. A full server restart does, because cooldowns are kept in memory.

Expired cooldown entries are deleted about once a minute.

## Loot

```lua
Config.Loot = {
    emptyChance = 52, -- % chance of nothing

    items = {
        { item = 'burger',   min = 1,  max = 1,  chance = 25 },
        { item = 'cash',     min = 10, max = 150, chance = 10 },
        { item = 'bandage',  min = 1,  max = 2,  chance = 10 },
        { item = 'lockpick', min = 1,  max = 1,  chance = 2 },
        { item = 'glass',    min = 1,  max = 1,  chance = 1 }
    }
}
```

The roll order:

1. If `items` is missing or empty, the search gives nothing.
2. A roll from 1 to 100 is compared with `emptyChance`. At 52, a little over half of searches are empty.
3. If it is not empty, one item is picked using `chance` as a weight against the other entries.
4. The amount is a random number from `min` to `max`.

`chance` is a weight, not a guaranteed percent, and it is only used after the empty roll fails. Raising one item's chance makes the others less common. It does not change `emptyChance`.

`item = 'cash'` or `item = 'money'` is added to the inventory when possible. If that fails, the same amount is paid as framework cash (QBCore / Qbox cash, ESX money).

You can give more than one item per search:

```lua
Config.Loot = {
    emptyChance = 40,
    minItems = 1,
    maxItems = 2,
    items = {
        { item = 'bandage', min = 1, max = 2, chance = 10 }
    }
}
```

Only item names that exist in your inventory will actually be given. A missing item is not created by this resource.

## Containers

`Config.Containers` defines the three types. Every type uses `Config.Loot` unless you add a `loot` table on that type.

| Key | In-game label | Props | Default search | Default cooldown |
|---|---|---|---|---|
| `large_dumpster` | Search Dumpster | 7 dumpster models | 10 seconds | 600 seconds |
| `trash_bin` | Search Trash Bin | 23 bin and recycle models | 10 seconds | 600 seconds |
| `scrap_skip` | Search Scrap Pile | 7 skip models | 10 seconds | 600 seconds |

Fields on each type:

| Field | Meaning |
|---|---|
| `label` | Locale key for the target / 3D text. |
| `progressLabel` | Locale key for the progress bar. |
| `icon` | Font Awesome icon used by the target option. |
| `searchTime` | Search length in milliseconds. `10000` is 10 seconds. The server rejects a reward claimed earlier than this. |
| `cooldown` | Seconds that this container stays locked after a successful search. Overrides `Config.DefaultCooldown`. |
| `anim.dict`, `anim.clip`, `anim.flag` | Animation played for the whole search. |
| `models` | Model names. Add a model name here to make that prop searchable. |

To add your own prop, put its model name inside the `models` list of the type it should behave as.

A container type can have its own loot:

```lua
large_dumpster = {
    label = 'target_search_dumpster',
    searchTime = 10000,
    cooldown = 600,
    loot = {
        emptyChance = 30,
        items = {
            { item = 'lockpick', min = 1, max = 1, chance = 10 }
        }
    },
    -- anim and models stay as they are
}
```

If `loot` is omitted, that type uses `Config.Loot`.

## Injuries

Injuries are on by default, even though `config.lua` does not contain a hazard block. Without gloves, each finished search has a 40% chance to deal damage equal to 10% of the player's usable health. Half of those hits are glass, half are scrap. Gloves in the inventory stop all of it.

At 1% health or less, a damaging hit has a 5% chance to kill the player. Otherwise health is left at a minimum of 1 so the cut itself is not fatal. No blood decal is applied.

Recognized glove items:

- `eldiven`
- `gloves`
- `glove`
- `scavenge_gloves`
- `work_gloves`

Add this block to `config.lua` to change or disable it:

```lua
Config.Hazards = {
    enabled = true,             -- false turns injuries off
    chanceWithoutGloves = 40,   -- percent
    damagePercent = 10,         -- percent of usable health
    gloves = { item = 'work_gloves' } -- extra item name to treat as gloves
}
```

The extra glove name is added to the list above. It does not replace that list.

## Locales

Text shown to players is in:

- `locales/en.json`
- `locales/tr.json`

Set `Config.Locale` to the file name without `.json`. If a key is missing, English is used.

To add a language, copy `locales/en.json` to `locales/de.json`, translate the values, and set `Config.Locale = 'de'`. Do not rename the keys. Keys such as `target_search_dumpster` are what `config.lua` points at.

`reward_item_entry` must keep both placeholders, in this order: amount, then item name.

```json
"reward_item_entry": "%dx %s"
```
