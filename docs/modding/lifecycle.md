---
layout: default
title: Plugin Lifecycle
parent: Modding
nav_order: 2
---

# Plugin Lifecycle

All Hytale server plugins must extend the `com.hypixel.hytale.server.core.plugin.PluginBase` class. This class provides the hook points for the server's lifecycle events.

## Lifecycle Methods

Your plugin should override the following methods to perform initialization and cleanup logic.

### 1. `setup()`
*   **When**: Called during the server boot phase, before the game loop starts.
*   **Purpose**: Register content, listeners, and commands. This is where you configure your plugin.
*   **Context**: Code run here blocks the server startup. Keep it fast.
*   **Key Actions**:
    *   Register Commands: `getCommandRegistry().registerCommand(...)`
    *   Register Event Listeners: `getEventRegistry().register(...)`
    *   Load Configuration: `withConfig(...)` or `getConfig()` (if available early).

```java
@Override
public void setup() {
    System.out.println("MyPlugin is setting up!");
    getEventRegistry().register(PlayerJoinEvent.class, event -> {
        System.out.println("Player joined: " + event.getPlayer().getName());
    });
}
```

### 2. `start()`
*   **When**: Called after the server has fully booted and is ready to accept connections (or slightly before).
*   **Purpose**: Start background tasks, initializing logic that depends on other systems being fully ready (like world generation or network).
*   **Context**: The server is effectively "running" now.
*   **Key Actions**:
    *   Start schedulers/tasks.
    *   Initialize heavy systems.
    *   Connect to external databases.

```java
@Override
public void start() {
    System.out.println("MyPlugin has started!");
}
```

### 3. `shutdown()`
*   **When**: Called when the server is stopping (gracefully).
*   **Purpose**: Clean up resources, save data, close connections.
*   **Key Actions**:
    *   Save pending data to disk.
    *   Close database connections.
    *   Cancel running tasks.

```java
@Override
public void shutdown() {
    System.out.println("MyPlugin is shutting down!");
}
```

## Plugin Manager
The `PluginManager` (`com.hypixel.hytale.server.core.plugin.PluginManager`) orchestrates these calls.
It ensures that dependencies are respected (if the dependency system is exposed) and that all plugins transition through `SETUP` -> `START` -> `STOP` in unison.
