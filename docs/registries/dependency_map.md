# Registry Dependency Map

This graph visualizes the relationships between Hytale's core registries and asset systems.

```mermaid
graph TD
    %% Core Registries
    AssetRegistry[Asset Registry]
    BlockStateRegistry[BlockState Registry]
    EntityRegistry[Entity Registry]
    CommandRegistry[Command Registry]
    EventRegistry[Event Registry]
    PacketRegistry[Packet Registry]

    %% Asset Types
    ItemAsset[Item Definition]
    BlockAsset[BlockType Definition]
    RecipeAsset[Crafting Recipe]
    ModelAsset[Model/Texture]
    SoundAsset[Sound Event]
    
    %% Runtime Components
    BlockState[BlockState (Runtime)]
    Entity[Entity (Runtime)]
    ItemStack[ItemStack (Runtime)]

    %% Relationships
    AssetRegistry -->|Loads| ItemAsset
    AssetRegistry -->|Loads| BlockAsset
    AssetRegistry -->|Loads| RecipeAsset
    AssetRegistry -->|Loads| ModelAsset

    BlockStateRegistry -->|Registers| BlockState
    BlockAsset -->|Defines Visuals For| BlockState
    BlockAsset -.->|References| ModelAsset
    
    EntityRegistry -->|Registers| Entity
    Entity -->|Uses| ModelAsset
    Entity -->|Uses| SoundAsset

    ItemAsset -->|Places| BlockAsset
    ItemAsset -->|Uses| ModelAsset
    ItemAsset -->|Harvests| BlockState
    
    RecipeAsset -->|Input/Output| ItemAsset
    RecipeAsset -->|Requires| BlockAsset
    
    ItemStack -->|Instance Of| ItemAsset
    
    %% Systems
    Plugin[Plugin Base]
    Plugin -->|Accesses| BlockStateRegistry
    Plugin -->|Accesses| EntityRegistry
    Plugin -->|Accesses| CommandRegistry
    Plugin -->|Accesses| EventRegistry
    
    Network[Network System]
    Network -->|Syncs| PacketRegistry
    PacketRegistry -- Syncs --> ItemAsset
    PacketRegistry -- Syncs --> BlockAsset
    PacketRegistry -- Syncs --> RecipeAsset

    style AssetRegistry fill:#f9f,stroke:#333,stroke-width:2px
    style BlockStateRegistry fill:#bbf,stroke:#333,stroke-width:2px
    style EntityRegistry fill:#bbf,stroke:#333,stroke-width:2px
```
