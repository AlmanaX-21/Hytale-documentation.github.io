# Using Registries

Hytale uses a Registry system to manage game content like Blocks, Entities, and Assets.

## Accessing Registries
Registries are accessed via `PluginBase`.

*   `getEntityRegistry()`: For registering new Entity types.
*   `getBlockStateRegistry()`: For registering Block States and behaviors.
*   `getAssetRegistry()`: For generic assets.

## Registering Content

### Entities
Use `EntityRegistry.registerEntity`.

```java
getEntityRegistry().registerEntity("my_mod:custom_zombie", CustomZombie.class, CustomZombie::new, CustomZombie.CODEC);
```

### Blocks
Use `BlockStateRegistry.registerBlockState`.

```java
getBlockStateRegistry().registerBlockState(MyBlockState.class, "my_mod:magic_stone", MyBlockState.CODEC);
```

### Generic Assets (Item, etc.)
Many systems use a `CodecMapRegistry`. You obtain this by passing the specific codec map to `getCodecRegistry`.

```java
// Example (Hypothetical pending deep dive into Item system)
// plugin.getCodecRegistry(ItemModule.ITEM_MAP_CODEC).register("my_mod:sword", MyItem.class, MyItem.CODEC);
```

## The `Codec` System
Almost all registerable content in Hytale uses the `Codec` system for serialization. This allows the game to save/load data and sync it over the network automatically.
When creating new content classes, you usually need to define a `Codec<T>`.

```java
public static final Codec<MyData> CODEC = Codec.of(MyData::new)
    .field("name", MyData::getName, Codec.STRING)
    .build();
```
