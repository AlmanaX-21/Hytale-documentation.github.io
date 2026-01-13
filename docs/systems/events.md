# Event System

The Hytale Event System enables plugins to react to actions within the server. It is built around the `EventBus` and `EventRegistry`.

## Key Classes
*   **`EventBus`**: The core dispatcher. Located at `com.hypixel.hytale.event.EventBus`.
*   **`EventRegistry`**: A safe wrapper for registering events, provided to plugins via `PluginBase`.
*   **`EventPriority`**: Enum controlling the order of execution (`LOW`, `NORMAL`, `HIGH`, etc.).

## Registering Listeners

Use `getEventRegistry()` in your `PluginBase` class to register listeners.

### Basic Registration
```java
getEventRegistry().register(PlayerJoinEvent.class, event -> {
    // Handle event
    Player player = event.getPlayer();
    player.sendMessage("Welcome!");
});
```

### Priority Registration
Handlers with higher priority run *first* (CHECK: Hytale's priority order - usually High runs first, or last? Assuming standard pattern, often High runs first or "Monitor" runs last. *Analysis Note: Priority values are shorts. Higher value usually means higher priority.*).

```java
getEventRegistry().register(EventPriority.HIGH, PlayerJoinEvent.class, event -> {
    // Runs before NORMAL priority
});
```

### Async Registration
For events that support asynchronous execution, use `registerAsync`. This is crucial for I/O operations to avoid freezing the main thread.

```java
getEventRegistry().registerAsync(AsyncPlayerChatEvent.class, event -> {
    // Perform async logic (e.g., toxicity check API)
    return CompletableFuture.completedFuture(event);
});
```

## Event Types
*   `PlayerJoinEvent`
*   `PlayerQuitEvent`
*   `BlockBreakEvent` (Predicted)
*   `BlockPlaceEvent` (Predicted)
*   (Check `com.hypixel.hytale.event` package for specific classes)

## Dispatching Events
You can dispatch your own custom events if you have an `EventBus` instance, or via a custom event handling system.
`EventBus.dispatchFor(eventClass, key)`

## Unregistering
The `EventRegistry` provided to plugins handles automatic unregistration when the plugin is unloaded. You typically do not need to manually unregister events.
