
# Hytale Server Technical Analysis

This directory contains a comprehensive technical analysis of the Hytale Server source code.

## Architecture
*   [Boot Sequence](architecture/boot_sequence.md): Analysis of the server startup process.
*   [Threading Model](architecture/threading_model.md): Overview of threads and concurrency.

## Protocols & Networking
*   [Network Protocol](protocol/overview.md): Packet structure, handshake, and key packet types.

## Registry System
*   [Registries Overview](registries/overview.md): How the game registers content (Blocks, Items, Entities).

## Modding & Plugins
*   [Plugin API](modding/plugin_api.md): How plugins are structured and accessed.
*   [Runtime Environment](modding/runtime_sandbox.md): Classloading and security for plugins.

## Game Systems
*   [ECS Architecture](systems/ecs.md): Data-oriented Entity Component System analysis.
*   [World Management](systems/world_management.md): Chunk loading, generation, and world hierarchy.
*   [Content Formats](systems/content_formats.md): JSON schemas for Blocks, Items, and Recipes.
