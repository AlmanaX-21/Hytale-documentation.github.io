---
layout: default
title: Threading Model
parent: Architecture
nav_order: 2
---

# Server Threading Model

Hytale Server uses a combination of standard Java concurrency tools and Netty's event loops.

## Core Threads

| Thread Name | Description |
| :--- | :--- |
| **Main** | The initial thread that handles boot, setup, and plugin initialization. |
| **Scheduler** | A Single Thread Scheduled Executor (`HytaleServer.SCHEDULED_EXECUTOR`) used for periodic tasks like config saving and backups. |
| **KeepAlive** | A daemon thread that keeps the JVM alive if other threads exit. |
| **ShutdownThread** | Handles the graceful shutdown sequence. |

## Concurrency Pools

### ForkJoinPool
The server configures the common `ForkJoinPool` to use `HytaleForkJoinThreadFactory`. This pool is likely used for parallel processing of tasks, such as chunk generation or heavy computation.

### Netty Event Loops
Network I/O is handled by Netty.
*   **Boss Group**: Accepts incoming connections.
*   **Worker Group**: Handles IO read/write operations.

## Async Operations
The code relies heavily on `CompletableFuture` for asynchronous operations, particularly in:
*   **World Loading**: `Universe.loadWorld` returns a `CompletableFuture`.
*   **Plugin Lifecycle**: `PluginBase.preLoad()` returns a `CompletableFuture`.

## Synchronization
*   **AssetRegistry.ASSET_LOCK**: A `ReentrantReadWriteLock` used when modifying asset registries.
*   **PluginManager.lock**: A `ReentrantReadWriteLock` protecting the plugin state and list.
