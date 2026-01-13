
# Hytale Content Formats (JSON Schemas)

Hytale uses a data-driven content system where assets are defined in JSON files. These files are loaded by the `AssetRegistry`.

## Blocks (`BlockType`)

**Path**: `assets/hytale/blocks/` (typically defined inside Items, but can be standalone in rare cases, though `BlockType.java` documentation says "Can only be defined within an **Item** and not standalone").

### Core Properties
*   **`Group`**: String. Used for BlockSets/Tech tags.
*   **`DrawType`**: Enum (`Liquids`, `Floral`, `Solid`, `Transparent`, etc.).
*   **`Material`**: `BlockMaterial`.
*   **`States`**: `StateData` (e.g., `facing=north`, `open=true`).
*   **`VoxelShape`**: `HitboxType`, `InteractionHitboxType`.

### Rendering
*   **`Textures`**: Array of `BlockTypeTextures`.
*   **`Model`**: `CustomModel` (reference to `.obj` or `.hym`?).
*   **`Light`**: `ColorLight` (RGB + Brightness).
*   **`Particles`**: `BlockParticleSetId`.

### Gameplay
*   **`Hardness`**: `BlockBreakingDecalId`.
*   **`Friction`**: `MovementSettings`.
*   **`Interactions`**: Map of `InteractionType` to scripts/actions.
*   **`Support`**: Structural integrity mechanics (`SupportDropType`, `MaxSupportDistance`).

## Items (`Item`)

**Path**: `assets/hytale/items/*.json`

### Core Properties
*   **`Id`**: Unique String identifier.
*   **`Name`**: Localized string key.
*   **`MaxStack`**: Integer.
*   **`Tags`**: List of tags (e.g., "sword", "wood").

### Block Definition
Hytale items often define their associated block inline.
*   **`Block`**: `BlockType` object (see above).
*   **`BlockId`**: Reference to an existing block.

### Visuals
*   **`Icon`**: Path to PNG.
*   **`Model`**: Path to 3D model.
*   **`Animation`**: Animation state.
*   **`Particles`**: Particle effects when held/equipped.

### Behavior
*   **`Consumable`**: Boolean.
*   **`Food`**: Hunger restoration.
*   **`Tool`**: Mining speed, harvest level.
*   **`Weapon`**: Damage, attack speed.
*   **`Armor`**: Protection values.

## Crafting Recipes (`CraftingRecipe`)

**Path**: `assets/hytale/recipes/*.json`

### Structure
*   **`Id`**: Unique ID.
*   **`Input`**: List of `MaterialQuantity` (Item ID + Amount).
*   **`Output`**: `MaterialQuantity`.
*   **`Station`**: `BenchRequirement` (e.g., "WorkBench", "Anvil").
*   **`TimeSeconds`**: Crafting time.
*   **`KnowledgeRequired`**: Boolean (needs unlocking?).
