---
name: unity-rpg-architecture-framework
description: The authoritative architecture and development standard for a production-scale Unity 6 (C#) data-driven RPG. Defines project structure, modular feature architecture, Hybrid ECS runtime, VContainer dependency injection, MessagePack serialization, Addressables asset pipeline, world-centric persistence, server-authoritative networking, coding standards, and implementation guidelines. All code generation, refactoring, and architectural decisions must conform to this specification.
---

# Unity 6 Data-Driven RPG Architecture Skill Standard

This document establishes the authoritative architecture, project structure, coding standards, runtime paradigms, and system designs for building an enterprise-grade Modular Data-Driven RPG in **Unity 6**.

---

## 1. Tech Stack & Architectural Overview

| Category | Technology / Paradigm | Notes |
| :--- | :--- | :--- |
| **Engine** | Unity 6 (C# 12 / .NET Standard 2.1+) | High-performance C# focus |
| **Architecture** | Modular + Data-Driven + Feature-Based | High cohesion, zero direct coupling between features |
| **Runtime Model** | Hybrid ECS + Component-Based | Pure data components + decoupled execution systems |
| **Patterns** | System + Service + EventBus + Repository | Clean separation of logic, state, messaging, and storage |
| **Dependency Injection**| **VContainer** | Explicit lifetime scopes (Root -> World -> Session -> Local) |
| **Networking** | Single Player + Host/Client Co-op | Server-Authoritative, Tick Sync, Client Prediction + Reconciliation |
| **Serialization** | **MessagePack** | Ultra-fast binary payload serialization |
| **Save System** | Repository + Atomic Transactions + Migrations | World-Centric state, Binary Data + JSON Metadata |
| **Game Data** | ScriptableObjects + CSV | Data Definitions compile to MessagePack/SO binary caches |
| **Asset Pipeline** | **Addressables** | Asynchronous memory-managed asset loading |
| **Platforms** | Windows / macOS (Steam Ready) | Multi-platform build optimization |

---

## 2. Directory Structure & Namespace Mapping

All source code must strictly follow the folder hierarchy and namespace mapping detailed below.

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
│   │   ├── Runtime/             --> Game.Features.Character.Runtime
│   │   ├── Definition/          --> Game.Features.Character.Definition
│   │   ├── Save/                --> Game.Features.Character.Save
│   │   └── UI/                  --> Game.Features.Character.UI
│   ├── Combat/                  --> Game.Features.Combat
│   │   ├── Runtime/
│   │   ├── SkillSystem/
│   │   ├── BuffSystem/
│   │   ├── StatusEffectSystem/
│   │   ├── ElementSystem/
│   │   ├── RaceSystem/
│   │   └── SizeSystem/
│   ├── Inventory/               --> Game.Features.Inventory
│   │   ├── Runtime/
│   │   ├── Definition/
│   │   ├── Refinement/
│   │   ├── Enchant/
│   │   ├── Equipment/
│   │   └── Card/
│   ├── World/                   --> Game.Features.World
│   │   ├── Runtime/
│   │   ├── Definition/
│   │   ├── Farming/
│   │   ├── Buildings/
│   │   ├── ResourceNodes/
│   │   ├── Weather/
│   │   ├── DayNight/
│   │   ├── Dungeon/
│   │   ├── Instance/
│   │   ├── WorldEvent/
│   │   └── Social/ (GuildSystem, PartySystem, TradeSystem)
│   ├── AI/                      --> Game.Features.AI
│   ├── Quest/                   --> Game.Features.Quest
│   ├── Craft/                   --> Game.Features.Craft
│   ├── Mercenary/               --> Game.Features.Mercenary
│   └── Achievement/             --> Game.Features.Achievement
│
├── Multiplayer/                 --> Game.Multiplayer
│   ├── Network/                 --> Game.Multiplayer.Network
│   ├── Authority/               --> Game.Multiplayer.Authority
│   ├── Tick/                    --> Game.Multiplayer.Tick
│   ├── Prediction/              --> Game.Multiplayer.Prediction
│   └── Replication/             --> Game.Multiplayer.Replication
│
├── Persistence/                 --> Game.Persistence
│   ├── CharacterSave/           --> Game.Persistence.CharacterSave
│   ├── WorldSave/               --> Game.Persistence.WorldSave
│   ├── Transaction/             --> Game.Persistence.Transaction
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
│   └── ... (Popup, Settings, etc.)
│
├── Data/                        --> Game.Data
│   ├── Definitions/
│   ├── Tables/
│   └── Constants/
│
├── Tools/                       --> Game.Tools (Editor / Pipeline)
│   ├── DataImporter/
│   ├── DataCompiler/
│   ├── SaveEditor/
│   ├── SaveValidator/
│   ├── MigrationTool/
│   └── DebugTools/
│
└── Content/                     --> Asset Bundles / Addressable Assets
    ├── Addressables/
    ├── Materials/
    ├── Fonts/
    └── Shaders/
```

---

## 3. Core Architecture Rules & Patterns

### 3.1 VContainer Dependency Injection (DI)
1. **Scope Hierarchy**:
   - `RootLifetimeScope`: Global Services (`AssetService`, `SaveService`, `AudioService`, `EventBus`, `NetworkService`).
   - `WorldLifetimeScope`: Spawned on loading a world session (`WorldState`, `ChunkSystem`, `SpawnSystem`, `ServerTick`).
   - `UILifetimeScope`: Child of Scene/World scopes for UI Controllers.
2. **Rules**:
   - **NEVER** use `GameObject.Find`, `FindObjectOfType`, or static singletons for core services.
   - Inject dependencies via `[Inject]` on constructors or `Construct()` methods on MonoBehaviour Views.
   - Interfaces must be bound: `builder.Register<SaveService>(Lifetime.Singleton).As<ISaveService>();`.

### 3.2 Hybrid ECS Runtime Paradigm
1. **Entity**: A lightweight identifier struct (`struct EntityId { public readonly ulong Value; }`) or container holding pure component data.
2. **Component**: Pure C# `struct` containing data only (no logic, no MonoBehaviour references).
3. **System**: C# classes implementing `ITickable` or `IFixedTickable`. Process batches of components sequentially.
4. **View**: MonoBehaviour attached to GameObjects strictly for visual rendering, animation, and collision output. Views listen to state changes or component outputs; they **never** execute business logic.

### 3.3 EventBus Messaging
- Completely decoupled pub/sub system operating via strongly typed C# `struct` events.
- All events implement `IEvent`.
- Subscriptions are managed using `IDisposable` tokens or automatically unwound during scope disposal.

### 3.4 Data Pipeline (ScriptableObject + CSV)
- **Definitions**: Designer-authored immutable data stored in CSV files or ScriptableObjects (`ItemDefinition`, `MonsterDefinition`).
- **Data Compiler**: Custom Editor tools parse CSVs, validate foreign keys, and bake them into fast binary MessagePack lookup maps or Addressable ScriptableObjects.
- **Runtime Instances**: Dynamic runtime structs (`ItemInstance`, `CharacterEntity`) containing references to static Definition IDs plus mutable runtime state (durability, refinement level, enchants).

---

## 4. Multiplayer & Networking Engine

### 4.1 Topology & Synchronization
- **Host / Client Co-op**: Host runs full server authority alongside a local client.
- **Tick-Based Engine**: Network logic executes at fixed rate (e.g., 30 Hz or 60 Hz) independent of visual rendering frame rate.
- **Payload Format**: Compressed MessagePack binary buffers passed over standard UDP sockets.

### 4.2 Client Prediction & Reconciliation Workflow
```
[Client Input] ---> Buffer local input frame (Tick T)
               ---> Send Input Packet to Host/Server
               ---> Predict local movement/actions immediately
               
[Server Tick]  ---> Process input frame (Tick T)
               ---> Compute authoritative state
               ---> Broadcast State Snapshot (Tick T)

[Client Receive] -> Compare server state (Tick T) with predicted state (Tick T)
                 -> IF Divergent: Snap to server state & Replay unacknowledged inputs (Tick T+1 .. Current)
                 -> IF Matching: Purge acknowledged input from buffer
```

---

## 5. Persistence & Save Data Boundaries

Save data is explicitly partitioned to prevent state corruption, enable cloud sync, and support character transfers between worlds.

### 5.1 Save File Hierarchy
```
Save/
├── Characters/
│   ├── Knight.dat          (Binary MessagePack Data)
│   ├── Wizard.dat
│   └── Archer.dat
├── Worlds/
│   ├── MyWorld.dat         (World-Centric Binary State)
│   ├── Hardcore.dat
│   └── Sandbox.dat
├── Metadata.json           (JSON Metadata for quick listing/UI display)
├── Backups/                (Timestamped rolling backups)
└── Settings.json           (Graphics, Input, Audio configurations)
```

### 5.2 Strict Data Boundary Matrix

| Data Category | Character Save (`.dat`) | World Save (`.dat`) | Runtime Only (Excluded) |
| :--- | :---: | :---: | :---: |
| **Base / Job Level & Stats** | ✅ Included | ❌ Excluded | ❌ |
| **Inventory, Equipment & Cards** | ✅ Included | ❌ Excluded | ❌ |
| **Skills & Shortcuts** | ✅ Included | ❌ Excluded | ❌ |
| **Character Quests & Achievements**| ✅ Included | ❌ Excluded | ❌ |
| **World Seed, Time & Weather** | ❌ Excluded | ✅ Included | ❌ |
| **Terrain, Buildings & Crops** | ❌ Excluded | ✅ Included | ❌ |
| **Chests, Resource Nodes & Spawns** | ❌ Excluded | ✅ Included | ❌ |
| **Guild & Party Core State** | ❌ Excluded | ✅ Included | ❌ |
| **Guild ID / Party ID / World ID** | ✅ Reference Only | ✅ Full State | ❌ |
| **Damage & Buff Timers** | ❌ Excluded | ❌ Excluded | ✅ Runtime Only |
| **Aggro, Prediction/Snapshot Buffers**| ❌ Excluded | ❌ Excluded | ✅ Runtime Only |

### 5.3 Transactional Save Protocol
Save operations must execute under an atomic transaction wrapper:
1. **Save Begin**: Lock target persistent repository.
2. **Temp Write**: Write serialized MessagePack payload to `.tmp` file.
3. **Validate**: Perform CRC checksum and data schema validation on `.tmp`.
4. **Commit**: Atomic replace of target `.dat` file with `.tmp` file.
5. **Rollback**: On failure, purge `.tmp`, restore previous `.dat` backup, and throw `SaveTransactionException`.

---

## 6. Implementation Blueprints & Code Examples

### Blueprint 1: Bootstrap & VContainer Scope Setup

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
            // Core Messaging
            builder.Register<EventBus>(Lifetime.Singleton).As<IEventBus>();

            // Global Services
            builder.Register<AssetService>(Lifetime.Singleton).As<IAssetService>();
            builder.Register<SaveService>(Lifetime.Singleton).As<ISaveService>();
            builder.Register<NetworkService>(Lifetime.Singleton).As<INetworkService>();
            builder.Register<AudioService>(Lifetime.Singleton).As<IAudioService>();

            // Entry Point
            builder.RegisterEntryPoint<Bootstrapper>();
        }
    }
}
```

```csharp
// File: Assets/Scripts/Bootstrap/Bootstrapper.cs
namespace Game.Bootstrap
{
    using VContainer.Unity;
    using Cysharp.Threading.Tasks;
    using Game.Core.Services;

    public class Bootstrapper : IAsyncStartable
    {
        private readonly IAssetService _assetService;
        private readonly ISaveService _saveService;

        public Bootstrapper(IAssetService assetService, ISaveService saveService)
        {
            _assetService = assetService;
            _saveService = saveService;
        }

        public async UniTask StartAsync(System.Threading.CancellationToken cancellation)
        {
            await _assetService.InitializeAsync();
            await _saveService.InitializeAsync();

            // Load Main Menu Scene via Addressables
            await _assetService.LoadSceneAsync("Scene_MainMenu");
        }
    }
}
```

---

### Blueprint 2: Type-Safe Zero-Allocation EventBus

```csharp
// File: Assets/Scripts/Core/Messaging/EventBus.cs
namespace Game.Core.Messaging
{
    using System;
    using System.Collections.Generic;

    public interface IEvent { }

    public interface IEventBus
    {
        void Publish<T>(T message) where T : struct, IEvent;
        IDisposable Subscribe<T>(Action<T> handler) where T : struct, IEvent;
    }

    public class EventBus : IEventBus
    {
        private readonly Dictionary<Type, object> _subscribers = new();

        public void Publish<T>(T message) where T : struct, IEvent
        {
            var type = typeof(T);
            if (_subscribers.TryGetValue(type, out var obj) && obj is Action<T> handlers)
            {
                handlers.Invoke(message);
            }
        }

        public IDisposable Subscribe<T>(Action<T> handler) where T : struct, IEvent
        {
            var type = typeof(T);
            if (!_subscribers.TryGetValue(type, out var existing))
            {
                _subscribers[type] = handler;
            }
            else
            {
                _subscribers[type] = (Action<T>)existing + handler;
            }

            return new SubscriptionToken(() => Unsubscribe(handler));
        }

        private void Unsubscribe<T>(Action<T> handler) where T : struct, IEvent
        {
            var type = typeof(T);
            if (_subscribers.TryGetValue(type, out var existing))
            {
                var current = (Action<T>)existing - handler;
                if (current == null) _subscribers.Remove(type);
                else _subscribers[type] = current;
            }
        }

        private class SubscriptionToken : IDisposable
        {
            private readonly Action _unsubscribeAction;
            public SubscriptionToken(Action unsubscribeAction) => _unsubscribeAction = unsubscribeAction;
            public void Dispose() => _unsubscribeAction?.Invoke();
        }
    }
}
```

---

### Blueprint 3: Feature Architecture — Combat & Stats System

```csharp
// File: Assets/Scripts/Features/Combat/Runtime/CombatData.cs
namespace Game.Features.Combat.Runtime
{
    using System;
    using System.Runtime.InteropServices;
    using MessagePack;

    public enum ElementType : byte { Neutral, Fire, Water, Earth, Wind, Holy, Shadow }
    public enum RaceType : byte { Formless, Undead, Brute, Plant, Insect, Fish, Demon, Humanoid }
    public enum SizeType : byte { Small, Medium, Large }

    [StructLayout(LayoutKind.Sequential)]
    public struct HealthComponent
    {
        public int CurrentHp;
        public int MaxHp;
    }

    [MessagePackObject]
    public struct DamageInstance
    {
        [Key(0)] public ulong AttackerId;
        [Key(1)] public ulong TargetId;
        [Key(2)] public int RawDamage;
        [Key(3)] public ElementType Element;
        [Key(4)] public RaceType AttackerRace;
        [Key(5)] public bool IsCritical;
    }
}
```

```csharp
// File: Assets/Scripts/Features/Combat/Runtime/FormulaSystem.cs
namespace Game.Features.Combat.Runtime
{
    using System;

    public static class FormulaSystem
    {
        public static int CalculateDamage(in DamageInstance dmg, float elementMultiplier, float sizeModifier)
        {
            float damage = dmg.RawDamage * elementMultiplier * sizeModifier;
            if (dmg.IsCritical) damage *= 1.4f;
            return Math.Max(1, (int)damage);
        }

        public static float GetElementModifier(ElementType attack, ElementType defense)
        {
            if (attack == ElementType.Fire && defense == ElementType.Earth) return 1.5f;
            if (attack == ElementType.Water && defense == ElementType.Fire) return 1.5f;
            return 1.0f;
        }
    }
}
```

---

### Blueprint 4: Persistence Repository & Migration Blueprint

```csharp
// File: Assets/Scripts/Persistence/CharacterSave/CharacterSaveData.cs
namespace Game.Persistence.CharacterSave
{
    using MessagePack;

    [MessagePackObject]
    public class CharacterSaveData
    {
        [Key(0)] public int Version { get; set; } = 1;
        [Key(1)] public string CharacterId { get; set; }
        [Key(2)] public string Name { get; set; }
        [Key(3)] public int BaseLevel { get; set; }
        [Key(4)] public int JobLevel { get; set; }
        [Key(5)] public long Experience { get; set; }

        // Reference Boundaries Only
        [Key(6)] public string WorldIdReference { get; set; }
        [Key(7)] public string GuildIdReference { get; set; }
        [Key(8)] public string PartyIdReference { get; set; }
    }
}
```

```csharp
// File: Assets/Scripts/Persistence/CharacterSave/CharacterRepository.cs
namespace Game.Persistence.CharacterSave
{
    using System.IO;
    using Cysharp.Threading.Tasks;
    using MessagePack;
    using UnityEngine;

    public class CharacterRepository
    {
        private readonly string _saveDirectory;

        public CharacterRepository()
        {
            _saveDirectory = Path.Combine(Application.persistentDataPath, "Save", "Characters");
            if (!Directory.Exists(_saveDirectory)) Directory.CreateDirectory(_saveDirectory);
        }

        public async UniTask SaveCharacterAsync(CharacterSaveData data)
        {
            string finalPath = Path.Combine(_saveDirectory, $"{data.CharacterId}.dat");
            string tempPath = finalPath + ".tmp";

            byte[] bytes = MessagePackSerializer.Serialize(data);

            // Transaction step 1: Temp Write
            await File.WriteAllBytesAsync(tempPath, bytes);

            // Transaction step 2: Validate
            if (File.Exists(tempPath) && new FileInfo(tempPath).Length > 0)
            {
                // Transaction step 3: Commit
                File.Move(tempPath, finalPath, overwrite: true);
            }
            else
            {
                if (File.Exists(tempPath)) File.Delete(tempPath);
                throw new IOException($"Save validation failed for character {data.CharacterId}");
            }
        }
    }
}
```

---

### Blueprint 5: Client Prediction & Input Ring Buffer (Netcode)

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

### Blueprint 6: Data Compiler Pipeline (ScriptableObject + CSV to Binary)

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
        [Key(0)] public int ItemId;
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

            for (int i = 1; i < lines.Length; i++) // Skip Header
            {
                var cols = lines[i].Split(',');
                items.Add(new ItemDefinitionBinary
                {
                    ItemId = int.Parse(cols[0]),
                    NameKey = cols[1],
                    ItemType = byte.Parse(cols[2]),
                    BasePrice = int.Parse(cols[3])
                });
            }

            byte[] bytes = MessagePackSerializer.Serialize(items);
            File.WriteAllBytes(outputPath, bytes);
            Debug.Log($"[DataCompiler] Successfully compiled {items.Count} items to {outputPath}");
        }
    }
}
```

---

## 7. Mandatory AI / Unity MCP Development Rules

When executing code generation, refactoring, or asset configuration via Unity MCP or automated coding workflows, you **MUST** strictly adhere to the following rules:

1. **No Static Singletons**: Do **not** create or suggest `Instance` static singletons. Always register services in a VContainer `LifetimeScope`.
2. **Strict Separation of Concerns**: Core systems and feature business logic must contain **zero** direct references to `UnityEngine.GameObject`, `UnityEngine.Transform`, or `MonoBehaviour`. All rendering components must be decoupled via View wrappers.
3. **Data Boundary Enforcement**:
   - Never write Guild/Party/World progress into `CharacterSaveData`.
   - Never store dynamic runtime buffers (such as `DamageInstance` or prediction history) in persistent save models.
4. **Asynchronous Execution**: Use **UniTask** for all asynchronous operations (file IO, asset loading, network requests). Avoid plain `System.Threading.Task` unless required by third-party libraries.
5. **Zero Allocation Hot Paths**: Combat execution, tick loops, and network prediction must allocate **zero** garbage per frame. Use structs, `in` parameters, and pooled memory buffers.
6. **MessagePack Annotation**: Every persistent struct/class must be annotated with `[MessagePackObject]` and explicit `[Key(n)]` attributes to support backward compatibility and schema migration.

---

## 8. Summary Verification Checklist

Before committing any feature code, verify against this checklist:

- [ ] Is the feature placed in the correct namespace (`Game.Features.[FeatureName]`)?
- [ ] Are all dependencies injected via VContainer without `FindObjectOfType` or `Instance` singletons?
- [ ] Is data serialized using MessagePack with schema versioning?
- [ ] Are character state and world state kept isolated in separate save files?
- [ ] Are UI controllers decoupled from raw logic via EventBus or UniRx/Observable properties?
- [ ] Are tick-based calculations isolated from `Time.deltaTime` rendering loops?
