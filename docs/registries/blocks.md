---
layout: default
title: Block Registry
parent: Registries
nav_order: 2
---

# Block Registry

The Block Registry system in Hytale handles the definition and lifecycle of blocks in the world. It is a dual-system consisting of:
1.  **BlockType (Asset)**: The static data definition (JSON) defining appearance, physics, and basic properties.
2.  **BlockState (Runtime)**: The logic class attached to a block in the world, handling persistence and complex behavior.

----------------------------------------
## SECTION A — REGISTRY DEFINITION
----------------------------------------

### 1. Definitions
*   **Runtime Registry**: `com.hypixel.hytale.server.core.universe.world.meta.BlockStateRegistry`
*   **Asset Type**: `com.hypixel.hytale.server.core.asset.type.blocktype.config.BlockType`
*   **Runtime Component**: `com.hypixel.hytale.server.core.universe.world.meta.BlockState`

### 2. Location
*   **File**: `com/hypixel/hytale/server/core/universe/world/meta/BlockStateRegistry.java`
*   **Namespace**: `hytale.blocks` (Implicit convention)

### 3. Data Model
**BlockType (Asset JSON)**
*   **Properties**: `material`, `opacity`, `light`, `textures`, `model`
*   **Serialization**: JSON via `AssetBuilderCodec`
*   **Hot Reload**: Supported via Asset System

**BlockState (Runtime Class)**
*   **Base Class**: `BlockState` (implements `Component<ChunkStore>`)
*   **Properties**: `position` (Vector3i), `chunk` (WorldChunk)
*   **Serialization**: BSON via `CodecMapCodec`.

### 4. Initialization
*   `BlockType` assets are loaded early by `AssetRegistryLoader`.
*   Rules in `BlockStateRegistry` are registered during Plugin `setup()` phase.

----------------------------------------
## SECTION B — REGISTRY MUTATION + MODDING OPS
----------------------------------------

### (1) Adding New Blocks
Blocks are added primarily by creating a JSON asset file. Complex behavior is added by overriding the `BlockState`.

**Action**: Register a custom BlockState class.
*   **Method**: `registerBlockState(Class<T> clazz, String key, Codec<T> codec)`
*   **Location**: `BlockStateRegistry` (accessible via `PluginBase.getBlockStateRegistry()`)

### (2) Modifying Existing Blocks
**Action**: Asset Overriding
*   Hytale's asset system allows overriding core assets by placing a file with the same path in your mod's asset pack.
*   Example: modifying `assets/hytale/config/block/grass.json` will override the vanilla grass block.

----------------------------------------
## SECTION C — CODE SNIPPETS
----------------------------------------

### Registering a Custom BlockState
If your block needs to store data (like a chest or machine), you register a custom `BlockState`.

```java
public class MagicRuneBlock extends BlockState {
    public static final Codec<MagicRuneBlock> CODEC = Codec.of(MagicRuneBlock::new)
        .field("power", MagicRuneBlock::getPower, Codec.INT)
        .build();

    private int power;

    public int getPower() { return power; }
}

// In Plugin.setup()
getBlockStateRegistry().registerBlockState(MagicRuneBlock.class, "my_mod:magic_rune", MagicRuneBlock.CODEC);
```

----------------------------------------
## SECTION D — SERIALIZATION + NETWORK SYNC
----------------------------------------

### BlockType Sync
*   **Packet**: `UpdateBlockTypes` (ID 0x28)
*   **Direction**: Server -> Client
*   **Content**: Sends all `BlockType` definitions to the client upon joining.
*   **Format**: Binary serialization of the `BlockType` fields (materials, textures, etc.).

### BlockState Sync
*   **Storage**: Saved to chunk data (BSON).
*   **Network**: `SetChunk` (0x83) or `ServerSetBlock` (0x8C).
*   **Format**: Serialized using the registered `Codec` for that BlockState class.

----------------------------------------
## SECTION E — REGISTRY HOOKING SURFACES
----------------------------------------

### 1. Tick Procedures
Blocks can register `TickProcedure`s in their JSON definition to have code run periodically.

### 2. Interactions
`BlockType` JSON defines interaction handlers (`Interactions` field). Listeners for these can be registered in the Event System.

----------------------------------------
## SECTION F — REGISTRY EXTENSION CONSTRAINTS
----------------------------------------

*   **Block IDs**: Internally mapped to short IDs (0-65535).
*   **Palette**: Chunk sections use a palette system.
*   **Parsing**: Strict JSON validation via `AssetBuilderCodec`. Missing required fields (like `Material`) will cause load failure.

----------------------------------------
## SECTION G — EXTERNAL DATA FORMATS
----------------------------------------

### BlockType JSON Schema (Partial)
```json
{
  "material": "ROCK",
  "opacity": "OPAQUE",
  "textures": [ ... ],
  "light": { "r": 255, "g": 200, "b": 100 },
  "flags": { "isSolid": true }
}
```

----------------------------------------
## SECTION H — SAFE MODDING WORKFLOWS
----------------------------------------

1.  **Define Assets First**: Create your `block_name.json` in your asset pack.
2.  **Register State Second**: If complex, register the `BlockState` in `setup()`.
3.  **Sync**: The server automatically handles syncing definitions to the client. Do not attempt to manually send BlockType packets.
