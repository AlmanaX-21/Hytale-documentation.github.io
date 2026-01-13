
# Hytale Plugin API

Hytale's modding system relies on **Plugins**. A plugin is a unit of code and assets that extends the game functionality.

## Plugin Structure

A Hytale plugin consists of:
1.  **`hytale-plugin.json`**: The manifest file describing the plugin.
2.  **Java Code**: Classes extending `JavaPlugin`.
3.  **Assets** (Optional): A `hytale.json` based asset pack (models, textures, etc.).

### Manifest (`hytale-plugin.json`)

The `PluginManifest` defines metadata and dependencies.

```json
{
  "group": "com.example",
  "name": "MyPlugin",
  "version": "1.0.0",
  "main": "com.example.MyPluginClass",
  "dependencies": {
    "Hytale:ImageLibrary": "1.0.0"
  },
  "includesAssetPack": true
}
```

*   **group/name**: Unique identifier (e.g., `com.example.MyPlugin`).
*   **main**: The main Java class extending `JavaPlugin`.
*   **includesAssetPack**: If true, the plugin loader expects assets in the same package/jar.

### The `JavaPlugin` Class

The entry point for your code. Use `com.hypixel.hytale.server.core.plugin.JavaPlugin`.

```java
public class MyPlugin extends JavaPlugin {
    public MyPlugin(JavaPluginInit init) {
        super(init);
    }

    @Override
    protected void setup() {
        // Called during setup phase
        // Register commands, listeners, etc.
    }

    @Override
    protected void start() {
        // Called when server starts
    }

    @Override
    protected void shutdown() {
        // Cleanup
    }
}
```

## Core Systems Access

The `PluginBase` class provides access to all major registries:

*   **`getCommandRegistry()`**: Register custom commands.
*   **`getEventRegistry()`**: Listen to game events.
*   **`getAssetRegistry()`**: Register custom assets (programmatically).
*   **`getEntityRegistry()`**: Manage entities.
*   **`getTaskRegistry()`**: Schedule tasks.
*   **`configs`**: Load configuration files via `withConfig()`.

### Configuration

Plugins can define strongly-typed configurations using Hytale's `Codec` system.

```java
Config<MyConfig> config = withConfig("config", MyConfig.CODEC);
```

## Events

Events are handled by the `EventRegistry`. You can listen to lifecycle events, ECS events, and more.

**Key Events:**
*   `BootEvent`: Server finished booting.
*   `ShutdownEvent`: Server shutting down.
*   `JoinWorld`: Player joined.
*   `PlaceBlockEvent`: Block placed.
*   `BreakBlockEvent`: Block broken.

## Asset Integration

Plugins can bundle assets directly. If `includesAssetPack` is true, the server loads assets from the plugin's jar/folder Structure:
*   `assets/`
    *   `hytale/`
        *   `textures/`
        *   `models/`

The `AssetRegistryLoader` automatically indexes these if the structure matches Hytale's asset format.
