
# World & Chunk Management

The world system in Hytale is hierarchical and heavily integrated with the ECS.

## Hierarchy

1.  **Universe** (`com.hypixel.hytale.server.core.universe.Universe`)
    *   The top-level container.
    *   Manages multiple `World` instances.
    *   Handles global player connections and routing.

2.  **World** (`com.hypixel.hytale.server.core.universe.world.World`)
    *   Represents a single dimension or instance.
    *   Contains two ECS stores: `EntityStore` (Game Objects) and `ChunkStore` (Map Data).
    *   Manages the game loop for that specific dimension.

3.  **ChunkStore** (`com.hypixel.hytale.server.core.universe.world.storage.ChunkStore`)
    *   A specialized ECS provider for identifying and loading chunks.
    *   Integrates with `IChunkLoader` (Disk IO) and `IWorldGen` (Generation).

## Chunk System

Chunks in Hytale are **Entities** within the `ChunkStore`.

*   **Identification**: Chunks are indexed by a `long` hash of their X, Z coordinates.
*   **Loading**: Asynchronous loading pipeline using `CompletableFuture`.
    *   `ChunkLoadState`: Tracks the status of a chunk (Loading, Generating, Ticking).
*   **Components**:
    *   `WorldChunk`: Contains metadata (index, flags).
    *   `ChunkColumn`: Contains the actual block data (sections).

## World Generation

*   **Interface**: `IWorldGen`.
*   **Process**:
    1.  `ChunkStore` requests a chunk.
    2.  If not on disk, `IWorldGen.generate()` is called.
    3.   Resulting `GeneratedChunk` is converted to a `Holder` (ECS entity template).
    4.  The new Chunk Entity is added to `ChunkStore`.

## Threading Model

*   **Ticking Thread**: Each World (or group of worlds) has a ticking thread.
*   **Async Loading**: Chunk I/O and Generation happen on separate thread pools to avoid stalling the tick loop.
