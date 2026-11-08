# Items and images

Two items are included with the resource. Everything else in the default loot table (`burger`, `cash`, `bandage`, `lockpick`) must already exist on your server.

| Name | Label | Weight | Stack | Used for |
|---|---|---|---|---|
| `glass` | Broken Glass | 100 | Yes | Loot |
| `eldiven` | Work Gloves | 150 | No | Full immunity to scavenging cuts while the item is in the inventory |

Ready-to-paste definitions are also in the resource:

- `install/ox_inventory.lua`
- `install/qb_inventory.lua`
- `install/qs_inventory.lua`
- `install/esx_inventory.sql`

Images, both 256×256 PNG with a transparent background:

- `install/images/eldiven.png`
- `install/images/glass.png`

Copy both files. The file name has to stay the same as the item name.

After adding items, restart the inventory resource, then `aastore_dumpster`.

## ox_inventory

1. Paste this into `ox_inventory/data/items.lua`, inside the returned table.
2. Copy both images into `ox_inventory/web/images/`.

```lua
['glass'] = {
    label = 'Broken Glass',
    weight = 100,
    stack = true,
    close = true,
    description = 'Sharp fragments of broken glass scavenged from trash.'
},

['eldiven'] = {
    label = 'Work Gloves',
    weight = 150,
    stack = false,
    close = true,
    description = 'Heavy-duty gloves providing 100% immunity against cuts and injuries while scavenging.'
},
```

ox_inventory uses the item name as the image file name, so `eldiven.png` and `glass.png` need no extra image field.

## qb-inventory / qb-core

1. Paste this into `qb-core/shared/items.lua`, inside `QBShared.Items`.
2. Copy both images into `qb-inventory/html/images/`.

```lua
['glass'] = {
    name = 'glass',
    label = 'Broken Glass',
    weight = 100,
    type = 'item',
    image = 'glass.png',
    unique = false,
    useable = false,
    shouldClose = true,
    combinable = nil,
    description = 'Sharp fragments of broken glass scavenged from dumpsters.'
},

['eldiven'] = {
    name = 'eldiven',
    label = 'Work Gloves',
    weight = 150,
    type = 'item',
    image = 'eldiven.png',
    unique = false,
    useable = false,
    shouldClose = true,
    combinable = nil,
    description = 'Heavy-duty gloves granting 100% protection against cuts when searching trash.'
},
```

If your inventory images live in a different folder, copy the PNGs there instead. The `image` field is the file name only.

## qs-inventory

1. Paste this into `qs-inventory/shared/items.lua`, inside `ItemList`.
2. Copy both images into `qs-inventory/html/images/`.

```lua
['glass'] = {
    name = 'glass',
    label = 'Broken Glass',
    weight = 100,
    type = 'item',
    image = 'glass.png',
    unique = false,
    useable = false,
    shouldClose = true,
    combinable = nil,
    description = 'Sharp fragments of broken glass scavenged from dumpsters.'
},

['eldiven'] = {
    name = 'eldiven',
    label = 'Work Gloves',
    weight = 150,
    type = 'item',
    image = 'eldiven.png',
    unique = false,
    useable = false,
    shouldClose = true,
    combinable = nil,
    description = 'Heavy-duty work gloves that prevent hand injuries when scavenging.'
},
```

## ESX Legacy

Run `install/esx_inventory.sql` in your database, or run this query. It matches the usual ESX Legacy `items` table. If your table uses different columns, change the query to match it before running.

```sql
INSERT INTO `items` (`name`, `label`, `weight`, `rare`, `can_remove`) VALUES
    ('glass', 'Broken Glass', 1, 0, 1),
    ('eldiven', 'Work Gloves', 1, 0, 1)
ON DUPLICATE KEY UPDATE `label` = VALUES(`label`);
```

ESX does not use the PNG files by itself. If you also run ox_inventory, qb-inventory, or qs-inventory on top of ESX, copy the images into that inventory as shown above.

## Other glove names

Protection is not limited to `eldiven`. Any of these item names in the inventory also blocks injury:

- `gloves`
- `glove`
- `scavenge_gloves`
- `work_gloves`

To accept one more name, add this to `config.lua`:

```lua
Config.Hazards = {
    enabled = true,
    gloves = { item = 'your_glove_item' }
}
```

The player does not need to "use" the gloves. Having one in the inventory when the search finishes is enough.
