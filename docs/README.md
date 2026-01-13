---
layout: default
title: Home
nav_order: 1
---

# Hytale Server Technical Analysis

This directory contains a comprehensive technical analysis of the Hytale Server source code.

## Architecture
*   [Boot Sequence](architecture/boot_sequence.md): Analysis of the server startup process.
*   [Threading Model](architecture/threading_model.md): Overview of threads and concurrency.

## Protocols & Networking
*   [Network Protocol](protocol/overview.md): Packet structure, handshake, and key packet types.

## Registry System
*   [Registries Overview](registries/overview.md): How the game registers content (Blocks, Items, Entities).
*   [Using Registries](registries/usage.md): Code examples for registering content.

## Modding & Plugins
*   [Plugin API](modding/plugin_api.md): How plugins are structured and accessed.
*   [Plugin Lifecycle](modding/lifecycle.md): Setup, Start, and Shutdown methods.
*   [Runtime Environment](modding/runtime_sandbox.md): Classloading and security for plugins.

## Game Systems
*   [ECS Architecture](systems/ecs.md): Data-oriented Entity Component System analysis.
*   [World Management](systems/world_management.md): Chunk loading, generation, and world hierarchy.
*   [Content Formats](systems/content_formats.md): JSON schemas for Blocks, Items, and Recipes.
*   [Movement States](systems/movement_states.md): Player movement tracking and physics on the server.
*   [Interaction System](systems/interactions.md): Data-driven player actions and effects.
*   [Event System](systems/events.md): Handling functionality via EventBus.
*   [Command System](systems/commands.md): Registering and executing commands.
