
# Hytale Network Protocol

Hytale uses a custom binary protocol built on top of Netty and QUIC.

## Packet Architecture

*   **Registry**: `com.hypixel.hytale.protocol.PacketRegistry`
*   **Interface**: `com.hypixel.hytale.protocol.Packet`
*   **Identification**: Packets are identified by an integer ID.
*   **Structure**: distinct concept of "Fixed Block" and "Variable Data" to optimize memory usage.

### Packet Structure
Many packets follow a specific serialization pattern:
1.  **Fixed Block**: A fixed-size header containing primitive data and *offsets* for variable data.
2.  **Variable Data**: Strings, Arrays, and nested objects appended after the fixed block.

This allows the server to validate packet size and structure efficiently before full deserialization.

## Packet List

Total Packets Registered: ~150+

### Connection & Auth
| ID | Name | Description |
| :--- | :--- | :--- |
| 0 | `Connect` | Handshake. Contains Protocol Hash, ClientType, UUID, Username, Auth Token. |
| 1 | `Disconnect` | Disconnect reason. |
| 2 | `Ping` | Latency check. |
| 3 | `Pong` | Latency response. |
| 11 | `AuthGrant` | Authentication success/grant. |
| 12 | `AuthToken` | Exchange auth tokens. |

### World State
| ID | Name | Description |
| :--- | :--- | :--- |
| 20 | `WorldSettings` | Initial world configuration. |
| 21 | `WorldLoadProgress` | Loading screen progress. |
| 23 | `RequestAssets` | Client requests asset pack. |
| 131 | `SetChunk` | Chunk data transmission. |
| 140 | `ServerSetBlock` | Single block change from server. |
| 141 | `ServerSetBlocks` | Batch block change. |

### Entities & Player
| ID | Name | Description |
| :--- | :--- | :--- |
| 51 | `UpdateEntityEffects` | Sync entity status/effects. |
| 104 | `JoinWorld` | Player joins the world. |
| 108 | `ClientMovement` | Player movement updates to server. |
| 109 | `ClientTeleport` | Teleport player. |
| 161 | `EntityUpdates` | Generic entity update packet. |
| 163 | `ChangeVelocity` | Apply velocity to entity. |

### Builder Tools (Creative)
Hytale has extensive protocol support for in-game building tools.
| ID | Name | Description |
| :--- | :--- | :--- |
| 400 | `BuilderToolArgUpdate` | Tool settings update. |
| 403 | `BuilderToolExtrudeAction` | Extrude operation. |
| 407 | `BuilderToolPasteClipboard` | Paste schematic. |
| 409 | `BuilderToolSelectionUpdate` | Update selection box. |

### Asset Editor
Packets 300-361 are dedicated to the live Asset Editor, allowing developers to modify assets (JSONs) in real-time and sync changes to the server.

## Handshake Flow

1.  **Client** sends `Connect` (ID 0).
2.  **Server** validates Protocol Hash and Auth.
3.  **Server** sends `AuthGrant` (ID 11) or `Disconnect` (ID 1).
4.  **Server** sends `WorldSettings` (ID 20).
5.  **Client** starts loading, Server sends `WorldLoadProgress` (ID 21).
6.  **Server** sends `JoinWorld` (ID 104) when ready.
7.  **Client** sends `ClientReady` (ID 105).
