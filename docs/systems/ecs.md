---
layout: default
title: ECS Architecture
parent: Systems
nav_order: 1
---

# Entity Component System (ECS) Architecture

Hytale's game logic is built upon a custom Entity Component System (ECS). This architecture separates data (Components) from logic (Systems) and identity (Entities), enabling high performance and modularity.

## Core Concepts

*   **Entities**: Represented by a `Ref<ECS_TYPE>`. They are lightweight handles that point to a specific index in the storage.
*   **Components**: Pure data classes (POJOs) implementing `Component<ECS_TYPE>`. They contain no logic.
*   **Systems**: Classes implementing `ISystem` that process entities possessing specific components.
*   **Store**: The central container managing entities and their components.

## The `Store` Class

`com.hypixel.hytale.component.Store<ECS_TYPE>` is the heart of the ECS.
*   **Archetype Storage**: Entities are grouped into "Archetypes" (unique combinations of components) and stored in `ArchetypeChunk`s. This is similar to Unity's DOTS or Bevy ECS, providing memory contiguity for cache efficiency.
*   **Parallel Processing**: The `Store` supports parallel task execution (`ParallelTask`), allowing systems to update entities concurrently across threads.
*   **Command Buffer**: Structural changes (adding/removing entities/components) are queued in `CommandBuffer`s and applied at safe points to prevent concurrency issues during iteration.

## Key System interfaces

*   **`ISystem`**: Base interface.
*   **`TickingSystem`**: Systems that run every tick.
*   **`EntityEventSystem`**: Reacts to entity-specific events.
*   **`WorldEventSystem`**: Reacts to global world events.
*   **`HolderSystem`**: Validates or processes entities when they are added to the store.

## Dual ECS Worlds

Hytale uses two distinct ECS worlds running in parallel for each game world:

1.  **Entity Store** (`EntityStore`):
    *   Manages game objects like Players, Mobs, Items, Projectiles.
    *   Components: `UUIDComponent`, `NetworkId`, `Transform`, `Velocity`, `Health`, etc.

2.  **Chunk Store** (`ChunkStore`):
    *   Manages map chunks as entities.
    *   Components: `WorldChunk` (metadata), `ChunkColumn` (block data).
    *   This allows the "World" itself to be managed and updated using the same modular system as entities.
