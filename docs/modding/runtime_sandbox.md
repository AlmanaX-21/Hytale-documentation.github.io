
# Plugin Runtime & Sandbox

Hytale plugins run in a managed Java environment with specific class loading rules to ensure stability and compatibility.

## Class Loading

*   **Loader**: `com.hypixel.hytale.server.core.plugin.PluginClassLoader`
*   **Isolation**: Plugins are loaded in their own `PluginClassLoader`.
    *   **Delegation Priority**:
        1.  **System/Server**: Core Hytale classes (to ensure types match).
        2.  **Self**: Classes inside the plugin's JAR.
        3.  **Bridge**: The `PluginManager.PluginBridgeClassLoader` connects plugins, allowing them to share classes if dependencies are declared.

## Security & Identification

*   **ThirdPartyPlugin**: Classes from external plugins are tagged with the "ThirdPartyPlugin" `ClassLoaderName`.
*   **Exception Tracking**: The server uses `isFromThirdPartyPlugin` checks in stack traces to attribute crashes to specific plugins.
*   **Permissions**: Plugins have a `basePermission` calculated from `"group.name"`. This interacts with the internal permission system (likely used for command access).

## Execution Model

*   **Thread**: Plugins run on the main server thread by default. Heavy operations should be offloaded to `HytaleServer.SCHEDULED_EXECUTOR` or `ForkJoinPool` to avoid lagging game ticks.
*   **Status**: `PluginState` (NONE, SETUP, START, ENABLED, DISABLED, SHUTDOWN) tracks the lifecycle. If a plugin throws an error during setup/start, it is automatically set to `DISABLED`.
