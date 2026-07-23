---
name: unity-rpg-architecture-framework
description: Production architecture, directory standards, netcode specs, save boundary matrix, and complete C# blueprints for Unity 6 Data-Driven RPG.
---

# Unity 6 Data-Driven RPG Master Architecture Skill Standard

This document is the authoritative technical standard and system design specification for building an enterprise-grade Modular Data-Driven RPG in **Unity 6 (C#)**.

---

## 1. Tech Stack & Architectural Overview

| Technical Domain | Specification / Standard | Implementation Notes |
| :--- | :--- | :--- |
| **Engine Core** | Unity 6 (C# 12 / .NET Standard 2.1+) | High-performance C# standard |
| **Architecture** | Modular + Data-Driven + Feature-Based | High cohesion, zero direct feature coupling |
| **Runtime Model** | Hybrid ECS + Component-Based | Pure data components (`struct`) + decoupled Systems (`ITickable`) |
| **Patterns** | System + Service + EventBus + Repository | Clear separation of logic, state, messaging, and storage |
| **Dependency Injection**| **VContainer** | Scoped lifetime hierarchy (`Root` -> `World` -> `UI`) |
| **Networking** | Single Player + Host/Client Co-op | Server-Authoritative, Tick Sync, Client Prediction + Reconciliation |
| **Serialization** | **MessagePack** | Ultra-fast binary payload serialization |
| **Save Format** | Binary Data (`.dat`) + JSON Metadata | World-Centric state, atomic transactions, version migrations |
| **Game Data** | ScriptableObjects + CSV | Definitions compile to MessagePack/SO binary caches |
| **Asset Pipeline** | **Addressables** | Memory-managed async asset loading (DLC / Mod friendly) |
| **Platforms** | Windows / macOS (Steam Ready) | Multi-platform build target optimization |

---

## 2. Complete Project Directory & Namespace Mapping

All source code in the repository must strictly follow this folder hierarchy and namespace mapping:

```
Assets/Scripts/
├── Bootstrap/                   --> Game.Bootstrap
│   ├── GameEntry.cs
│   ├── Bootstrapper.cs
│   ├── LifetimeScope.cs
│   ├── DependencyInjection/
│   ├── RuntimeContext.cs
│   ├── GameState.cs
│   ├── Config/
│   └── GlobalEvents.cs
│
├── Core/                        --> Game.Core
│   ├── Framework/               --> Game.Core.Framework
│   │   ├── GameObjectPool.cs
│   │   ├── ObjectFactory.cs
│   │   ├── UpdateLoop.cs
│   │   ├── TickSystem.cs
│   │   └── TimeProvider.cs
│   ├── Services/                --> Game.Core.Services
│   │   ├── SaveService.cs
│   │   ├── NetworkService.cs
│   │   ├── AudioService.cs
│   │   ├── LocalizationService.cs
│   │   ├── ConfigService.cs
│   │   └── AssetService.cs
│   ├── Systems/                 --> Game.Core.Systems
│   │   ├── SceneSystem.cs
│   │   ├── UISystem.cs
│   │   ├── InputSystem.cs
│   │   ├── CameraSystem.cs
│   │   └── ResourceSystem.cs
│   ├── Messaging/               --> Game.Core.Messaging
│   │   ├── EventBus.cs
│   │   ├── EventChannel.cs
│   │   └── EventRegistry.cs
│   ├── Interfaces/              --> Game.Core.Interfaces
│   ├── Utilities/               --> Game.Core.Utilities
│   ├── Extensions/              --> Game.Core.Extensions
│   └── Constants/               --> Game.Core.Constants
│
├── Features/                    --> Game.Features.[FeatureName]
│   ├── Character/               --> Game.Features.Character
│   │   ├── Runtime/             (CharacterEntity, CharacterState, StatsComponent, LevelComponent, InventoryComponent, EquipmentComponent, SkillComponent, CostumeComponent)
│   │   ├── Definition/          (JobDefinition, GrowthDefinition, StatDefinition)
│   │   ├── Save/                (CharacterSaveData)
│   │   └── UI/
│   ├── Combat/                  --> Game.Features.Combat
│   │   ├── Runtime/             (CombatState, HealthComponent, DamageComponent, AttackSystem, DamageSystem, FormulaSystem)
│   │   ├── SkillSystem/
│   │   ├── BuffSystem/
│   │   ├── StatusEffectSystem/
│   │   ├── ElementSystem/
│   │   ├── RaceSystem/
│   │   └── SizeSystem/
│   ├── Inventory/               --> Game.Features.Inventory
│   │   ├── Runtime/             (ItemInstance, InventoryState, StorageState)
│   │   ├── Definition/          (ItemDefinition, EquipmentDefinition, CardDefinition, MaterialDefinition)
│   │   ├── Refinement/
│   │   ├── Enchant/
│   │   ├── Equipment/
│   │   └── Card/
│   ├── World/                   --> Game.Features.World
│   │   ├── Runtime/             (WorldState, WorldEntity, ChunkSystem, SpawnSystem, WorldRuleSystem)
│   │   ├── Definition/          (MapDefinition, MonsterDefinition, NPCDefinition, DungeonDefinition)
│   │   ├── Farming/
│   │   ├── Buildings/
│   │   ├── ResourceNodes/
│   │   ├── Weather/
│   │   ├── DayNight/
│   │   ├── Dungeon/
│   │   ├── Instance/
│   │   ├── WorldEvent/
│   │   └── Social/              (GuildSystem, PartySystem, TradeSystem)
│   ├── AI/                      --> Game.Features.AI (AIController, BehaviorTree, Navigation, Targeting, DecisionSystem)
│   ├── Quest/                   --> Game.Features.Quest
│   ├── Craft/                   --> Game.Features.Craft
│   ├── Mercenary/               --> Game.Features.Mercenary
│   └── Achievement/             --> Game.Features.Achievement
│
├── Multiplayer/                 --> Game.Multiplayer
│   ├── Network/                 (NetworkManager, Connection, Packet, RPC, Session)
│   ├── Authority/               (ServerAuthority, Ownership, Validation, AntiCheat)
│   ├── Tick/                    (ServerTick, ClientTick, NetworkTime, Interpolation)
│   ├── Prediction/              (ClientPrediction, Reconciliation, InputBuffer)
│   └── Replication/             (Snapshot, StateSync, EntityReplication)
│
├── Persistence/                 --> Game.Persistence
│   ├── CharacterSave/           (Repository, Serializer, Loader, Validator, Versioning, Migration)
│   ├── WorldSave/               (Repository, Serializer, Loader, Validator, Versioning, Migration, Guild, Party, WorldSocialState)
│   ├── Transaction/             (SaveBegin, TempWrite, Validate, Commit, Rollback)
│   ├── Backup/
│   ├── Compression/
│   ├── Encryption/
│   └── AutoSave/
│
├── UI/                         --> Game.UI
│   ├── MainMenu/
│   ├── CharacterSelect/
│   ├── WorldSelect/
│   ├── Lobby/
│   ├── HUD/
│   ├── Inventory/
│   ├── Equipment/
│   ├── Skills/
│   ├── Quest/
│   ├── Party/
│   ├── Guild/
│   ├── Trade/
│   ├── Craft/
│   ├── Settings/
│   └── Popup/
│
├── Data/                        --> Game.Data
│   ├── Definitions/             (Items, Equipment, Cards, Jobs, Skills, Monsters, NPC, Maps, Quests, Craft)
│   ├── Tables/                  (DropTables, FormulaTables, SpawnTables, Localization)
│   └── Constants/
│
├── Tools/                       --> Game.Tools (Editor / Pipeline)
│   ├── DataImporter/
│   ├── DataCompiler/
│   ├── SaveEditor/
│   ├── SaveValidator/
│   ├── MigrationTool/
│   ├── VersionGenerator/
│   ├── AssetBuilder/
│   ├── ModBuilder/
│   └── DebugTools/
│
└── Content/                     --> Asset Bundles / Addressable Assets
    ├── Addressables/            (Characters, Monsters, NPC, Maps, WorldChunks, Buildings, UI, Audio, Effects, DLC, Mods)
    ├── Materials/
    ├── Fonts/
    └── Shaders/
```

---

## 3. VContainer Dependency Injection Standard

### Scope Hierarchy
1. **RootLifetimeScope** (Global Services, persists across scenes):
   - Global Services: `AssetService`, `SaveService`, `AudioService`, `EventBus`, `NetworkService`, `ConfigService`.
   - Entry Point: `Bootstrapper`.
2. **WorldLifetimeScope** (World Session Lifetime):
   - Services: `WorldState`, `ChunkSystem`, `SpawnSystem`, `ServerTick`, `ClientPrediction`.
3. **UILifetimeScope** (Per UI Screen / Presenter):
   - ViewModels, Presenters, View Controllers.

```csharp
// File: Assets/Scripts/Bootstrap/RootLifetimeScope.cs
namespace Game.Bootstrap
{
    using VContainer;
    using VContainer.Unity;
    using Game.Core.Services;
    using Game.Core.Messaging;

    public class RootLifetimeScope : LifetimeScope
    {
        protected override void Configure(IContainerBuilder builder)
        {
            builder.Register<EventBus>(Lifetime.Singleton).As<IEventBus>();
            builder.Register<AssetService>(Lifetime.Singleton).As<IAssetService>();
            builder.Register<SaveService>(Lifetime.Singleton).As<ISaveService>();
            builder.Register<NetworkService>(Lifetime.Singleton).As<INetworkService>();
            builder.Register<AudioService>(Lifetime.Singleton).As<IAudioService>();

            builder.RegisterEntryPoint<Bootstrapper>();
        }
    }
}
```

---

## 4. Multiplayer Netcode & Prediction Protocol

### Server Authority & Tick Sync
- **Fixed Rate Ticks**: Server operates at fixed tick rate (30 Hz / 60 Hz).
- **Client Prediction Loop**:
  1. Local Client captures `CharacterInputCmd` for Tick T.
  2. Input is buffered into local `InputBuffer[T]`.
  3. Client predicts movement immediately on local view.
  4. Server receives `CharacterInputCmd`, computes authoritative position, and broadcasts `EntityStateSnapshot`.
  5. If Client predicted position deviates > 0.05m, Client snaps to Server Position(T) and replays unacknowledged inputs (T+1 .. Current).

```csharp
// File: Assets/Scripts/Multiplayer/Prediction/InputBuffer.cs
namespace Game.Multiplayer.Prediction
{
    using UnityEngine;
    using MessagePack;

    [MessagePackObject]
    public struct CharacterInputCmd
    {
        [Key(0)] public uint Tick;
        [Key(1)] public Vector2 MoveInput;
        [Key(2)] public bool SkillPressed;
        [Key(3)] public ushort SkillId;
    }

    public class InputBuffer
    {
        private const int BUFFER_SIZE = 128;
        private readonly CharacterInputCmd[] _buffer = new CharacterInputCmd[BUFFER_SIZE];

        public void StoreInput(in CharacterInputCmd input)
        {
            int index = (int)(input.Tick % BUFFER_SIZE);
            _buffer[index] = input;
        }

        public ref readonly CharacterInputCmd GetInput(uint tick)
        {
            int index = (int)(tick % BUFFER_SIZE);
            return ref _buffer[index];
        }
    }
}
```

---

## 5. Persistence & Save Data Boundaries

Save files are partitioned to allow characters to travel across different multiplayer worlds.

```
Save/
├── Characters/             (Knight.dat, Wizard.dat)
├── Worlds/                 (MyWorld.dat, Sandbox.dat)
├── Metadata.json           (Fast UI List Display)
├── Backups/                (Rolling Timestamped Backups)
└── Settings.json           (Graphics, Input, Audio)
```

### Strict Data Boundary Matrix

| Data Category | Character Save (`.dat`) | World Save (`.dat`) | Runtime Only (Excluded) |
| :--- | :---: | :---: | :---: |
| **Base / Job Level & Stats** | ✅ Included | ❌ Excluded | ❌ |
| **Inventory, Equipment & Cards** | ✅ Included | ❌ Excluded | ❌ |
| **Refinement & Enchants** | ✅ Included | ❌ Excluded | ❌ |
| **Skills & Shortcut Config** | ✅ Included | ❌ Excluded | ❌ |
| **Quests & Achievements** | ✅ Included | ❌ Excluded | ❌ |
| **World Seed, Time & Weather** | ❌ Excluded | ✅ Included | ❌ |
| **Terrain, Buildings & Crops** | ❌ Excluded | ✅ Included | ❌ |
| **Resource Nodes, Spawns & Chests** | ❌ Excluded | ✅ Included | ❌ |
| **Guild & Party Core State** | ❌ Excluded | ✅ Included | ❌ |
| **Guild ID / Party ID References** | ✅ Ref ID Only | ✅ Full State | ❌ |
| **Damage, Buff Timers & Aggro** | ❌ Excluded | ❌ Excluded | ✅ Runtime Only |
| **Prediction & Snapshot Buffers**| ❌ Excluded | ❌ Excluded | ✅ Runtime Only |

### Atomic Transaction Protocol
1. **Temp Write**: Serialize payload to target path + `.tmp`.
2. **Validate**: Verify file non-zero byte length and MessagePack CRC checksum.
3. **Commit**: Replace active `.dat` file with `.tmp` atomically.
4. **Rollback**: On failure, purge `.tmp`, restore backup from `Backups/`, throw `SaveTransactionException`.

```csharp
// File: Assets/Scripts/Persistence/CharacterSave/CharacterRepository.cs
namespace Game.Persistence.CharacterSave
{
    using System.IO;
    using Cysharp.Threading.Tasks;
    using MessagePack;

    public class CharacterRepository
    {
        private readonly string _saveDirectory;

        public CharacterRepository(string saveDirectory)
        {
            _saveDirectory = saveDirectory;
            if (!Directory.Exists(_saveDirectory)) Directory.CreateDirectory(_saveDirectory);
        }

        public async UniTask SaveCharacterAsync(CharacterSaveData data)
        {
            string finalPath = Path.Combine(_saveDirectory, $"{data.CharacterId}.dat");
            string tempPath = finalPath + ".tmp";

            byte[] bytes = MessagePackSerializer.Serialize(data);
            await File.WriteAllBytesAsync(tempPath, bytes);

            if (File.Exists(tempPath) && new FileInfo(tempPath).Length > 0)
            {
                File.Move(tempPath, finalPath, overwrite: true);
            }
            else
            {
                if (File.Exists(tempPath)) File.Delete(tempPath);
                throw new IOException($"Save transaction failed for character {data.CharacterId}");
            }
        }
    }
}
```

---

## 6. Implementation Blueprints & C# Code Examples

### Blueprint: Equipment, Card & Refinement Data Model

```csharp
// File: Assets/Scripts/Features/Character/Runtime/EquipmentInstance.cs
namespace Game.Features.Character.Runtime
{
    using MessagePack;

    [MessagePackObject]
    public struct EquipmentInstance
    {
        [Key(0)] public ushort EquipmentDefinitionId;
        [Key(1)] public byte RefinementLevel;       // e.g. +10 Refinement
        [Key(2)] public ushort[] SocketedCardIds;   // Socketed Cards
        [Key(3)] public ushort EnchantOptionId;     // Random Bonus Enchant

        public readonly bool HasSockets => SocketedCardIds != null && SocketedCardIds.Length > 0;
    }
}
```

### Blueprint: CSV to Binary MessagePack Compiler Tool

```csharp
// File: Assets/Scripts/Tools/DataCompiler/ItemDataCompiler.cs
namespace Game.Tools.DataCompiler
{
    using System.Collections.Generic;
    using System.IO;
    using UnityEngine;
    using MessagePack;

    [MessagePackObject]
    public struct ItemDefinitionBinary
    {
        [Key(0)] public ushort ItemId;
        [Key(1)] public string NameKey;
        [Key(2)] public byte ItemType;
        [Key(3)] public int BasePrice;
    }

    public static class ItemDataCompiler
    {
        public static void CompileCsvToBinary(string csvPath, string outputPath)
        {
            var lines = File.ReadAllLines(csvPath);
            var items = new List<ItemDefinitionBinary>();

            for (int i = 1; i < lines.Length; i++)
            {
                if (string.IsNullOrWhiteSpace(lines[i])) continue;
                var cols = lines[i].Split(',');

                items.Add(new ItemDefinitionBinary
                {
                    ItemId = ushort.Parse(cols[0]),
                    NameKey = cols[1],
                    ItemType = byte.Parse(cols[2]),
                    BasePrice = int.Parse(cols[3])
                });
            }

            byte[] bytes = MessagePackSerializer.Serialize(items);
            File.WriteAllBytes(outputPath, bytes);
            Debug.Log($"[DataCompiler] Compiled {items.Count} items to {outputPath}");
        }
    }
}
```

---

## 7. Mandatory Rules for AI Agents

1. **No Static Singletons**: Never create `public static T Instance { get; set; }`. Always use VContainer DI.
2. **Strict View Decoupling**: Core logic and component structs must have **zero** references to `UnityEngine.GameObject` or `MonoBehaviour`. Use `IEventBus` struct messages.
3. **Zero Allocation Hot Paths**: Pass structs using `in` parameters (`in DamageInstance dmg`). Avoid LINQ inside `Tick()` loops.
4. **MessagePack Key Annotations**: Explicitly decorate every serialized class/struct with `[MessagePackObject]` and explicit `[Key(n)]` attributes.
