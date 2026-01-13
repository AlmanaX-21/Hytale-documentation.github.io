---
layout: default
title: Entity Registry
parent: Registries
nav_order: 3
---

# Entity Registry

The Entity Registry manages the definitions of dynamic game objects (Players, Monsters, Projectiles). Hytale's entity system is heavily integrated with its ECS (Entity Component System) architecture.

----------------------------------------
## SECTION A — REGISTRY DEFINITION
----------------------------------------

### 1. Definitions
*   **Runtime Registry**: `com.hypixel.hytale.server.core.modules.entity.EntityRegistry`
*   **Base Class**: `com.hypixel.hytale.server.core.entity.Entity`
*   **Module**: `com.hypixel.hytale.server.core.modules.entity.EntityModule`

### 2. Location
*   **File**: `com/hypixel/hytale/server/core/modules/entity/EntityRegistry.java`
*   **Namespace**: `hytale.entities` (Implicit)

### 3. Data Model
**Entity Class**
*   **Storage**: Implements `Component<EntityStore>`. Entities are essentially "Fat Components" stored in the world's `EntityStore`.
*   **Identity**:
    *   `networkId` (int): Transient runtime ID (server-assigned).
    *   `legacyUuid` (UUID): Persistent ID.
*   **Serialization**: Uses `BuilderCodec<Entity>` (BSON).

### 4. Initialization
*   Custom entities are registered during the `setup()` phase of a Plugin.
*   `EntityModule` maintains the mapping between String IDs ("hytale:zombie") and Java Classes.

----------------------------------------
## SECTION B — REGISTRY MUTATION + MODDING OPS
----------------------------------------

### (1) Adding New Entities
To add a new entity, you must create a class extending `Entity` and register it.

**Action**: Register Entity Class
*   **Method**: `registerEntity(String key, Class<T> clazz, Function<World, T> constructor, DirectDecodeCodec<T> codec)`
*   **Location**: `EntityRegistry` (`plugin.getEntityRegistry()`)

### (2) Removing/Overriding
*   **Removal**: Not directly supported via public API in `EntityRegistry`.
*   **Overriding**: Possible if `EntityModule` allows overwriting keys (Unverified - assume forbidden/unsafe).

----------------------------------------
## SECTION C — CODE SNIPPETS
----------------------------------------

### Defining a Custom Entity

```java
public class CustomZombie extends Entity {
    public static final DirectDecodeCodec<CustomZombie> CODEC = Codec.of(CustomZombie::new)
        .field("CustomData", CustomZombie::getCustomData, Codec.STRING)
        .build();

    private String customData;

    public CustomZombie(World world) {
        super(world);
    }

    public String getCustomData() { return customData; }
}
```

### Registering the Entity

```java
@Override
public void setup() {
    getEntityRegistry().registerEntity(
        "my_mod:custom_zombie",
        CustomZombie.class,
        CustomZombie::new,
        CustomZombie.CODEC
    );
}
```

----------------------------------------
## SECTION D — SERIALIZATION + NETWORK SYNC
----------------------------------------

### Persistence
*   Entities are saved to chunk data (BSON) using their registered `Codec`.
*   The `EntityType` string (e.g., "my_mod:custom_zombie") is stored in the BSON document to identify which class to instantiate upon loading.

### Network Sync
*   **Spawning**: `JoinWorld` (ID 0x68) for players, `EntityUpdates` (ID 0xA1) for others.
*   **Movement**: `ClientMovement` / `ChangeVelocity`.
*   **Components**: `UpdateEntityEffects` syncs status effects and other component data.

----------------------------------------
## SECTION E — REGISTRY HOOKING SURFACES
----------------------------------------

### Lifecycle Hooks
*   `loadIntoWorld(World world)`: Called when added to the world.
*   `remove()`: Called when the entity is being removed/killed. Dispatches `EntityRemoveEvent`.
*   `clone()`: Used for template instantiation.

### Events
*   `EntityRemoveEvent`: Fired when an entity is removed.

----------------------------------------
## SECTION F — EXTENSION CONSTRAINTS
----------------------------------------

*   **Constructors**: Must usually accept `(World world)` or have a factory method that does.
*   **Codecs**: Must be stateless and thread-safe.
*   **IDs**: `networkId` is managed by the server (EntityStore). Do not manually assign.

----------------------------------------
## SECTION G — EXTERNAL DATA FORMATS
----------------------------------------

*   Entities are code-driven, but often use `Model` assets.
*   `ModelReference` codec key: `Model`.
*   `DisplayName` codec key: `DisplayName`.

----------------------------------------
## SECTION H — SAFE MODDING WORKFLOWS
----------------------------------------

1.  **Register Early**: Always register entities in `setup()`.
2.  **Versioning**: If you change your Entity's fields, you must handle BSON versioning in your Codec or previous saves may fail to load.
3.  **Client Sync**: Ensure the client has the corresponding model and texture assets (handled by Asset Pack syncing).
