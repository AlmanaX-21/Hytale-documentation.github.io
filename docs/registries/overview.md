
# Hytale Registries

Hytale uses a unified `AssetRegistry` system to manage game content. All content types (Blocks, Items, Sounds, Particles) are treated as "Assets" loaded from JSON files.

## Registry Architecture

*   **Manager**: `com.hypixel.hytale.assetstore.AssetRegistry`
*   **Loader**: `com.hypixel.hytale.server.core.asset.AssetRegistryLoader`
*   **Storage**: Each type has an `AssetStore` (e.g., `AssetStore<String, BlockType>`).
*   **Serialization**: Uses `AssetCodec` for JSON serialization/deserialization.
*   **Sync**: `AssetPacketGenerator` classes handle syncing registry data to clients.

## Core Registries

The following table lists the primary registries found in the server source.

| Asset Class | Path in Assets | Key Function | Dependencies |
| :--- | :--- | :--- | :--- |
| **BlockType** | `Item/Block/Blocks` | `getId` | *Loads After*: BlockBoundingBoxes, BlockSoundSet, SoundEvent, BlockParticleSet, etc. |
| **Item** | `Item/Items` | `getId` | *Loads After*: ItemCategory, ResourceType, BlockType, EntityEffect, SoundEvent, etc. |
| **CraftingRecipe** | `Item/Recipes` | `getId` | *Loads After*: Item, BlockType |
| **Fluid** | `Item/Block/Fluids` | `getId` | *Loads After*: FluidFX, BlockSoundSet, SoundEvent |
| **Environment** | `Environments` | `getId` | *Loads After*: Weather, FluidFX, ParticleSystem |
| **Weather** | `Weathers` | `getId` | *Loads After*: ParticleSystem |
| **SoundEvent** | `Audio/SoundEvents` | `getId` | *Loads After*: AudioCategory |
| **ParticleSystem** | `Particles` | `getId` | *Loads After*: ParticleSpawner |
| **EntityEffect** | `Entity/Effects` | `getId` | *Loads After*: ModelAsset, SoundEvent, DamageCause |
| **DamageCause** | `Entity/Damage` | `getId` | *Loads Before*: Item, Interaction |
| **ModelAsset** | `Models` | `getId` | *Loads After*: ParticleSystem, SoundEvent |

## Detailed Registry Information

### Blocks (`BlockType`)
*   **Source**: `com.hypixel.hytale.server.core.asset.type.blocktype.config.BlockType`
*   **Codec**: `BlockType.CODEC`
*   **Default Assets**: `BlockType.EMPTY`, `BlockType.UNKNOWN`, `BlockType.DEBUG_CUBE`
*   **Network Sync**: `BlockTypePacketGenerator` (Packet ID 40: `UpdateBlockTypes`)

### Items (`Item`)
*   **Source**: `com.hypixel.hytale.server.core.asset.type.item.config.Item`
*   **Codec**: `Item.CODEC`
*   **Default Assets**: `Item.UNKNOWN`
*   **Network Sync**: `ItemPacketGenerator` (Packet ID 54: `UpdateItems`)

### Entities
*   *Note*: Entity types seem to be defined via `EntityEffect`? Or `PrefabListAsset`? Further investigation is needed for the Entity Type registry (`EntityTypes` equivalent).

## Loading Process

1.  `AssetRegistryLoader.init()` initializes the `AssetRegistry`.
2.  `AssetRegistry.register()` calls register all `AssetStore` builders.
3.  `AssetRegistryLoader.loadAssets()` scans the `Server` directory in the asset pack.
4.  It resolves dependencies (DAG) to ensure assets load in the correct order.
5.  It loads JSON files, decodes them using `AssetCodec`, and stores them in the `AssetStore`.
