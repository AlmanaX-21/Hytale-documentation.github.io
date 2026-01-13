---
layout: default
title: Recipe Registry
parent: Registries
nav_order: 5
---

# Recipe Registry

Crafting Recipes in Hytale are defined as JSON assets. They link input items to output items and specify the crafting station (Bench) requirements.

----------------------------------------
## SECTION A — REGISTRY DEFINITION
----------------------------------------

### 1. Definitions
*   **Asset Class**: `com.hypixel.hytale.server.core.asset.type.item.config.CraftingRecipe`
*   **Packet Class**: `com.hypixel.hytale.protocol.CraftingRecipe`
*   **Asset Store**: `AssetRegistry.getAssetStore(CraftingRecipe.class)`

### 2. Location
*   **File**: `com/hypixel/hytale/server/core/asset/type/item/config/CraftingRecipe.java`
*   **Namespace**: `hytale.recipes` (Implicit)

### 3. Data Model
**Recipe Asset (JSON)**
*   **Input**: Array of `MaterialQuantity` (Item ID + Amount).
*   **Output**: `primaryOutput` and optional extra `outputs`.
*   **Requirements**: `BenchRequirement` (which bench is needed, e.g., "Crafting", "Fieldcraft").
*   **Progresion**: `knowledgeRequired`, `requiredMemoriesLevel`.

### 4. Initialization
*   Loaded by `AssetRegistryLoader` from the assets directory (typically `recipes/` folder within an asset pack).

----------------------------------------
## SECTION B — REGISTRY MUTATION + MODDING OPS
----------------------------------------

### (1) Adding New Recipes
**Action**: Create a JSON file in your asset pack.
*   **Format**: See Code Snippets.
*   **Auto-Discovery**: The game automatically discovers recipe files in the asset path.

### (2) Modifying Recipes
**Action**: Asset Override.
*   Override vanilla recipes by placing a file with the same name/path in your mod's asset pack.

----------------------------------------
## SECTION C — CODE SNIPPETS
----------------------------------------

### Basic Recipe JSON
```json
{
  "id": "my_mod:magic_sword_recipe",
  "input": [
    { "item": "hytale:stick", "amount": 2 },
    { "item": "my_mod:magic_bar", "amount": 1 }
  ],
  "primaryOutput": { "item": "my_mod:magic_sword", "amount": 1 },
  "benchRequirement": [
    {
      "type": "Crafting",
      "id": "hytale:workbench"
    }
  ]
}
```

### Fieldcraft Recipe (No Bench)
```json
{
  "id": "my_mod:torch_recipe",
  "input": [ ... ],
  "primaryOutput": { ... },
  "benchRequirement": [
    { "id": "Fieldcraft" }
  ]
}
```

----------------------------------------
## SECTION D — SERIALIZATION + NETWORK SYNC
----------------------------------------

*   **Packet**: Part of the Asset Registry Sync.
*   **Class**: `CraftingRecipe` (Protocol).
*   **Data**: Sends inputs, outputs, time to craft, and requirements to the client for the Crafting UI.

----------------------------------------
## SECTION E — EXTENSION CONSTRAINTS
----------------------------------------

*   **Bench IDs**: Must match known bench identifiers (e.g., `hytale:workbench`, `hytale:anvil`).
*   **Item IDs**: Must be valid.
*   **Knowledge**: If `knowledgeRequired` is true, players might not see the recipe until unlocked via the progression system (Memories).

----------------------------------------
## SECTION F — SAFE WORKFLOWS
----------------------------------------

1.  **Unique IDs**: Give your recipes unique IDs, different from the item ID if possible (e.g., `my_item_recipe` vs `my_item`).
2.  **Validation**: Ensure all input/output items exist.
3.  **Balance**: Consider the `timeSeconds` field for crafting duration.
