---
layout: default
title: Item Registry
parent: Registries
nav_order: 4
---

# Item Registry

Items in Hytale are primarily data-driven assets defined in JSON. The Item Registry is part of the broader Asset System.

----------------------------------------
## SECTION A — REGISTRY DEFINITION
----------------------------------------

### 1. Definitions
*   **Asset Class**: `com.hypixel.hytale.server.core.asset.type.item.config.Item`
*   **Packet Class**: `com.hypixel.hytale.protocol.ItemBase`
*   **Asset Store**: `AssetRegistry.getAssetStore(Item.class)`

### 2. Location
*   **File**: `com/hypixel/hytale/server/core/asset/type/item/config/Item.java`
*   **Namespace**: `hytale.items` (Implicit in asset path)

### 3. Data Model
**Item Asset (JSON)**
*   **Core**: `id` (String), `maxStack` (int), `rarity/quality`.
*   **Appearance**: `model`, `texture`, `icon`, `animation`.
*   **Behavior (Components)**:
    *   `Tool`: Mining speed, harvest levels.
    *   `Weapon`: Damage, attack speed.
    *   `Armor`: Protection values.
    *   `Block`: If this item places a block (`blockId`).
    *   `Consumable`: Food/Potion mechanics.

### 4. Initialization
*   Items are loaded by the `AssetRegistryLoader` from `assets/[namespace]/items/`.
*   Everything is fully data-driven; no Java code is required to add a standard item.

----------------------------------------
## SECTION B — REGISTRY MUTATION + MODDING OPS
----------------------------------------

### (1) Adding New Items
**Action**: Create a JSON file in your asset pack.
*   **Path**: `assets/my_mod/items/my_sword.json`
*   **Format**: See Code Snippets.

### (2) Modifying Existing Items
**Action**: Asset Override.
*   Place a file at the same relative path as the vanilla item (e.g., `assets/hytale/items/sword.json`) to overwrite its properties (e.g., damage, texture).

----------------------------------------
## SECTION C — CODE SNIPPETS
----------------------------------------

### Basic Item JSON
```json
{
  "id": "my_mod:super_sword",
  "maxStack": 1,
  "model": "my_mod:models/item/super_sword",
  "icon": "my_mod:textures/ui/icons/super_sword",
  "weapon": {
    "damage": 10,
    "attackSpeed": 1.5
  }
}
```

### Block Item JSON
```json
{
  "id": "my_mod:magic_stone_item",
  "blockId": "my_mod:magic_stone",
  "maxStack": 64
}
```

----------------------------------------
## SECTION D — SERIALIZATION + NETWORK SYNC
----------------------------------------

### Network Sync
*   **Packet**: Part of the Asset Registry Sync (Packet ID 0x28 or similar asset bundle sync).
*   **Class**: `ItemBase` is the DTO (Data Transfer Object) sent to clients.
*   **Method**: `Item.toPacket()` converts the complex server-side Asset into the lightweight `ItemBase` format.

### Storage
*   Items in inventories are referenced by their String ID.
*   NBT/Data: Individual interactions with items (durability, custom names) are stored in `ItemStack` data, not the `Item` definition itself.

----------------------------------------
## SECTION E — REGISTRY HOOKING SURFACES
----------------------------------------

### Interactions
*   `interactions`: Map of `InteractionType` to script/handler keys.
*   `OnEquip` / `OnUse`: Can be hooked via the Event System listening for `PlayerUseItemEvent`.

----------------------------------------
## SECTION F — SAFE MODDING WORKFLOWS
----------------------------------------

1.  **JSON First**: Do not try to register items via Java code unless absolutely necessary (e.g., dynamic generation). Use JSON.
2.  **Textures/Models**: Ensure referenced assets verify. strict validation will fail the load if a texture is missing.
3.  **Naming**: Use a clear namespace (`my_mod:item_name`) to avoid collisions.
