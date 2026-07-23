# AI Agent Guidelines & Project Standard: Unity 6 Data-Driven RPG Framework

This file specifies the authoritative guidelines, architectural rules, code standards, directory conventions, and operational workflows for any AI Agent working on this repository. All code generation, modifications, refactorings, and system designs must strictly comply with this document and the associated `SKILL.md` specification.

---

## 1. Project Overview & Technology Stack

- **Engine**: Unity 6 (C# 12 / .NET Standard 2.1+)
- **Architecture**: Modular + Data-Driven + Feature-Based
- **Runtime Model**: Hybrid ECS + Component-Based
- **Core Patterns**: System + Service + EventBus + Repository + StateMachine + ObjectPool
- **Dependency Injection**: VContainer (Strict Lifetime Scopes)
- **UI Binding**: Reactive Data Binding (R3 / UniRx / Observable Properties)
- **Localization**: Multi-language support via `ILocalizationService` with string key lookups
- **Networking**: Single Player + Host/Client Co-op (Server Authority, Tick-Based Sync, Client Prediction & Reconciliation)
- **Serialization**: MessagePack (Ultra-fast binary data)
- **Save System**: Repository + Transaction + Version Migration (World-Centric Persistent State: Binary `.dat` + JSON Metadata)
- **Game Data**: ScriptableObject + CSV (Definitions compile to MessagePack/SO binary caches)
- **Asset Pipeline**: Addressables (Asynchronous memory-managed assets with strict Handle disposal)
- **Logging**: Centralized via `ILoggerService` (Configurable log levels, disabled in production builds)
- **Testing**: Unity Test Framework (NUnit) for pure logic systems, formulas, and migration steps
- **Platforms**: Windows / macOS (Steam Ready, Dedicated Server Future Ready)

---

## 2. Directory Structure & Namespace Mapping

All C# source files inside `Assets/Scripts/` MUST follow this exact directory structure and corresponding namespace convention:

```
Assets/Scripts/
│
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
│   │   ├── LoggerService.cs
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
│   │   │   ├── CharacterEntity.cs
│   │   │   ├── CharacterState.cs
│   │   │   ├── StatsComponent.cs
│   │   │   ├── LevelComponent.cs
│   │   │   ├── InventoryComponent.cs
│   │   │   ├── EquipmentComponent.cs
│   │   │   ├── SkillComponent.cs
│   │   │   └── CostumeComponent.cs
│   │   ├── Definition/          --> Game.Features.Character.Definition
│   │   │   ├── JobDefinition.cs
│   │   │   ├── GrowthDefinition.cs
│   │   │   └── StatDefinition.cs
│   │   ├── Save/                --> Game.Features.Character.Save
│   │   │   └── CharacterSaveData.cs
│   │   └── UI/                  --> Game.Features.Character.UI
│   │
│   ├── Combat/                  --> Game.Features.Combat
│   │   ├── Runtime/ (CombatState, HealthComponent, DamageComponent, AttackSystem, DamageSystem, FormulaSystem)
│   │   ├── SkillSystem/
│   │   ├── BuffSystem/
│   │   ├── StatusEffectSystem/
│   │   ├── ElementSystem/
│   │   ├── RaceSystem/
│   │   └── SizeSystem/
│   │
│   ├── Inventory/               --> Game.Features.Inventory
│   │   ├── Runtime/ (ItemInstance, InventoryState, StorageState)
│   │   ├── Definition/ (ItemDefinition, EquipmentDefinition, CardDefinition, MaterialDefinition)
│   │   ├── Refinement/
│   │   ├── Enchant/
│   │   ├── Equipment/
│   │   └── Card/
│   │
│   ├── World/                   --> Game.Features.World
│   │   ├── Runtime/ (WorldState, WorldEntity, ChunkSystem, SpawnSystem, WorldRuleSystem)
│   │   ├── Definition/ (MapDefinition, MonsterDefinition, NPCDefinition, DungeonDefinition)
│   │   ├── Farming/
│   │   ├── Buildings/
│   │   ├── ResourceNodes/
│   │   ├── Weather/
│   │   ├── DayNight/
│   │   ├── Dungeon/
│   │   ├── Instance/
│   │   ├── WorldEvent/
│   │   └── Social/ (GuildSystem, PartySystem, TradeSystem)
│   │
│   ├── AI/                      --> Game.Features.AI (AIController, BehaviorTree, Navigation, Targeting, DecisionSystem)
│   ├── Quest/                   --> Game.Features.Quest
│   ├── Craft/                   --> Game.Features.Craft
│   ├── Mercenary/               --> Game.Features.Mercenary
│   └── Achievement/             --> Game.Features.Achievement
│
├── Multiplayer/                 --> Game.Multiplayer
│   ├── Network/                 --> Game.Multiplayer.Network (NetworkManager, Connection, Packet, RPC, Session)
│   ├── Authority/               --> Game.Multiplayer.Authority (ServerAuthority, Ownership, Validation, AntiCheat)
│   ├── Tick/                    --> Game.Multiplayer.Tick (ServerTick, ClientTick, NetworkTime, Interpolation)
│   ├── Prediction/              --> Game.Multiplayer.Prediction (ClientPrediction, Reconciliation, InputBuffer)
│   └── Replication/             --> Game.Multiplayer.Replication (Snapshot, StateSync, EntityReplication)
│
├── Persistence/                 --> Game.Persistence
│   ├── CharacterSave/           --> Game.Persistence.CharacterSave (Repository, Serializer, Loader, Validator, Versioning, Migration)
│   ├── WorldSave/               --> Game.Persistence.WorldSave (Repository, Serializer, Loader, Validator, Versioning, Migration, Guild, Party, WorldSocialState)
│   ├── Transaction/             --> Game.Persistence.Transaction (SaveBegin, TempWrite, Validate, Commit, Rollback)
│   ├── Backup/
│   ├── Compression/
│   └── Encryption/
│   └── AutoSave/
│
├── UI/                          --> Game.UI
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
│   ├── Definitions/ (Items, Equipment, Cards, Jobs, Skills, Monsters, NPC, Maps, Quests, Craft)
│   ├── Tables/ (DropTables, FormulaTables, SpawnTables, Localization)
│   └── Constants/
│
├── Tools/                       --> Game.Tools (DataImporter, DataCompiler, SaveEditor, SaveValidator, MigrationTool, VersionGenerator, AssetBuilder, ModBuilder, DebugTools)
│
└── Content/                     --> Addressable Assets & Raw Resources
    ├── Addressables/ (Characters, Monsters, NPC, Maps, WorldChunks, Buildings, UI, Audio, Effects, DLC, Mods)
    ├── Materials/
    ├── Fonts/
    └── Shaders/
```

---

## 3. Architecture Rules & Component Principles

### 3.1 Dependency Injection (VContainer)
- **Hierarchy of Lifetime Scopes**:
  1. `RootLifetimeScope`: Global singletons (`AssetService`, `SaveService`, `NetworkService`, `AudioService`, `LoggerService`, `LocalizationService`, `EventBus`).
  2. `WorldLifetimeScope`: Session/world state services (`WorldState`, `ChunkSystem`, `SpawnSystem`, `ServerTick`, `GameObjectPool`). Destroyed on scene unload.
  3. `UILifetimeScope`: UI controllers and view-models bound to an active scene or popup context.
- **Injection Rules**:
  - NEVER use `GameObject.Find`, `FindObjectOfType`, or static `Instance` singletons.
  - Inject dependencies strictly via constructor injection or `[Inject]` on MonoBehaviour views.
  - Register services by interface: `builder.Register<SaveService>(Lifetime.Singleton).As<ISaveService>();`.

### 3.2 Hybrid ECS Runtime Model
1. **Entity**: Lightweight struct identifier (`struct EntityId { public readonly ulong Value; }`) or container holding pure component structs.
2. **Component**: Pure C# `struct` carrying data only (no methods, no MonoBehaviour references).
3. **System**: Decoupled C# classes implementing `ITickable` / `IFixedTickable`. Execute processing loops on component batches.
4. **View**: MonoBehaviour components responsible solely for visuals, animations, audio, and physics collisions. Views read component data; they NEVER execute business/game logic.

### 3.3 Object Pooling Policy
- **Rule**: Frequently created and destroyed gameplay objects (e.g., visual effects, projectiles, damage numbers, Floating UI popups) MUST use `IGameObjectPool` / `IObjectFactory` registered in `Core/Framework/`. Direct calls to `Instantiate()` or `Destroy()` during gameplay loops are strictly prohibited.

### 3.4 Centralized Logging Standard
- **Rule**: Direct calls to `UnityEngine.Debug.Log()` inside Core and Feature logic are prohibited. All logging must route through `ILoggerService` injected via VContainer using appropriate severity levels (`LogDebug`, `LogInfo`, `LogWarning`, `LogError`). This allows logs to be completely compiled out or suppressed in Release builds.

### 3.5 Localization Rule
- **Rule**: Never hardcode user-facing text strings in C# code. All UI labels, item names, skill descriptions, and quest text must use string localization keys (`string`) and retrieve localized text via `ILocalizationService.GetText(key)`.

### 3.6 Reactive Data Binding (UI & State)
- **Rule**: UI Controllers must observe model changes via Reactive Properties (R3/UniRx) or `EventBus` events. UI elements must **NEVER** poll state inside `Update()`.

### 3.7 Messaging (EventBus)
- Zero-allocation pub/sub event pipeline using strongly typed C# structs implementing `IEvent`.
- Subscriptions return `IDisposable` tokens for clean lifecycle management during scope disposal.

### 3.8 Game State Machine Flow
Application state transitions follow a strict linear state machine:
```
[Bootstrapper] ──> [MainMenuState] ──> [LobbyState] ──> [WorldLoadingState] ──> [GameplayState]
                                                               │                       │
                                                      (Spawns WorldScope)     (Destroys WorldScope)
```

---

## 4. Multiplayer & Networking Engine

- **Topology**: Single Player + Host/Client Co-op with Server Authority.
- **Entity Allocation**: `EntityId` values are generated exclusively by the Server/Host authority and replicated to clients.
- **Tick Engine**: Fixed network rate (e.g., 30/60 Hz) independent of frame rendering (`Update`).
- **Payload**: MessagePack binary buffers over UDP sockets.
- **Client Prediction & Reconciliation**:
  1. Client buffers inputs in a ring buffer (`InputBuffer`) and predicts state immediately.
  2. Host/Server receives inputs, executes tick logic, and broadcasts authoritative state snapshots.
  3. Client compares server snapshot at tick $T$ with local predicted state at tick $T$. If mismatched, client snaps to server state and replays unacknowledged inputs from $T+1$ to current tick.

---

## 5. Persistence & Save Data Boundaries

Save data is strictly partitioned into independent binary files to enable character porting, cloud sync, and prevent world corruption.

```
Save/
├── Characters/
│   ├── Knight.dat
│   ├── Wizard.dat
│   └── Archer.dat
├── Worlds/
│   ├── MyWorld.dat
│   ├── Hardcore.dat
│   └── Sandbox.dat
├── Metadata.json
├── Backups/
└── Settings.json
```

### Data Boundary Matrix

| Data Category | Character Save (`.dat`) | World Save (`.dat`) | Runtime Only (Excluded) |
| :--- | :---: | :---: | :---: |
| **Character Profile, Level, Job, Stats** | ✅ Included | ❌ Excluded | ❌ |
| **Inventory, Equipment, Cards, Refinement, Enchant** | ✅ Included | ❌ Excluded | ❌ |
| **Skills, Skill Shortcuts, Storage, Currency** | ✅ Included | ❌ Excluded | ❌ |
| **Character Quests & Achievements** | ✅ Included | ❌ Excluded | ❌ |
| **World Seed, World Time, Weather** | ❌ Excluded | ✅ Included | ❌ |
| **Buildings, Farming, Resource Nodes, Chests** | ❌ Excluded | ✅ Included | ❌ |
| **NPC State, Monster Spawns, Boss/Dungeon Progress** | ❌ Excluded | ✅ Included | ❌ |
| **Guild Data, Storage, Skills & Member State** | ❌ Excluded | ✅ Included | ❌ |
| **Party Data, Members & Settings** | ❌ Excluded | ✅ Included | ❌ |
| **World ID / Guild ID / Party ID** | ✅ Reference Only | ✅ Full State | ❌ |
| **Damage Instances, Buff Timers, Aggro** | ❌ Excluded | ❌ Excluded | ✅ Runtime Only |
| **Spawn Queue, Prediction Buffer, Interpolation** | ❌ Excluded | ❌ Excluded | ✅ Runtime Only |

### Save Data Versioning & Migration Strategy
- Every persistent model (`CharacterSaveData`, `WorldSaveData`) must contain a `[Key(0)] public int Version { get; set; }`.
- When adding, modifying, or deprecating fields in a persistent model, increment `Version` by 1.
- Provide a corresponding `IMigrationStep` implementation in `Persistence/.../Migration` to upgrade older save payloads seamlessly without data corruption.

### Transactional Save Protocol
All save operations must be wrapped in atomic transactions:
1. **Save Begin**: Lock target persistent repository.
2. **Temp Write**: Write serialized MessagePack payload to `.tmp` file.
3. **Validate**: Perform CRC checksum and schema validation on `.tmp`.
4. **Commit**: Atomically replace `.dat` file with `.tmp`.
5. **Rollback**: On error, purge `.tmp`, restore backup, and throw `SaveTransactionException`.

---

## 6. Asset & Memory Lifecycle (Addressables)

- **Handle Tracking**: All Addressable handles loaded within a `LifetimeScope` MUST be registered to a `CompositeDisposable` or bound to the scope's `CancellationToken`.
- **Automatic Cleanup**: Upon unloading a world or scope destruction, all associated handles must be explicitly released via `Addressables.Release()` to prevent memory leaks.

---

## 7. Naming & File Conventions

| Concept | File / Class Pattern | Example | Type / Inheritance |
| :--- | :--- | :--- | :--- |
| **Service Interface** | `I[Name]Service.cs` | `ISaveService.cs` | `interface` |
| **Service Implementation** | `[Name]Service.cs` | `SaveService.cs` | `class` |
| **Pure Component** | `[Name]Component.cs` | `HealthComponent.cs` | `struct` |
| **System** | `[Name]System.cs` | `DamageSystem.cs` | `class, ITickable` |
| **View (MonoBehaviour)** | `[Name]View.cs` | `CharacterView.cs` | `MonoBehaviour` |
| **UI Controller** | `[Name]Controller.cs` | `InventoryController.cs` | `class, IInitializable` |
| **Save Data Model** | `[Name]SaveData.cs` | `CharacterSaveData.cs` | `class [MessagePackObject]` |
| **Migration Step** | `[Name]MigrationV[N].cs` | `CharacterMigrationV2.cs` | `IMigrationStep` |
| **Static Definition** | `[Name]Definition.cs` | `ItemDefinition.cs` | `ScriptableObject` / `struct` |
| **Typed Event** | `[Name]Event.cs` | `DamageDealtEvent.cs` | `struct, IEvent` |
| **Unit Test Class** | `[Name]Tests.cs` | `FormulaSystemTests.cs` | NUnit Test Class |

---

## 8. Mandatory Rules for AI Agents

When authoring, refactoring, or generating code for this repository:

1. **Strict Namespace Allocation**: Match the C# namespace to the file directory.
2. **No Static Singletons**: Never generate static singletons or `Instance` properties. Always use VContainer DI.
3. **Separation of Logic & Views**: Core logic systems must contain zero references to `UnityEngine.GameObject`, `Transform`, or `MonoBehaviour`.
4. **Data Boundary Compliance**: Never place World, Guild, or Party progress into `CharacterSaveData`.
5. **Asynchronous Execution**: Always use **UniTask** for async operations (`IAssetService`, `ISaveService`, network routines).
6. **Zero Allocation Hot Paths**: Avoid allocations in tick loops, damage calculations, and network prediction code. Use structs and `in` parameter modifiers.
7. **MessagePack Formatting**: All persistent structures must have `[MessagePackObject]` and explicit `[Key(index)]` attributes.
8. **Reactive UI**: Never write state polling in UI `Update()`. Use R3/UniRx reactive subscriptions.
9. **Addressable Disposal**: Always bind Addressable handle cleanup to LifetimeScope cancellation tokens.
10. **Structured Exception Bounds**: All background UniTasks and Tick loops must use `try-catch` blocks logging via `ILoggerService` to prevent silent thread crashes.
11. **Object Pooling Enforcement**: Use `IGameObjectPool` for all temporary VFX, projectiles, and damage UI popups. Direct `Instantiate()` in gameplay hot paths is prohibited.
12. **Centralized Logging**: Route all debug messages through `ILoggerService`. Direct `Debug.Log()` calls are forbidden in Core/Feature modules.
13. **No Hardcoded Strings**: Use string keys and `ILocalizationService` for all user-facing text.
14. **Save Migration**: Always increment `Version` and add an `IMigrationStep` when altering persistent save models.
15. **Unit Test Requirement**: Pure C# systems (such as `FormulaSystem`, math algorithms, CSV data compilers, and save migrations) MUST include accompanying NUnit unit tests in `Tests/`.
16. **Zero Error & Verification Gate**: The AI Agent MUST NEVER present code for user review if there are unresolved compilation errors, syntax errors, failing tests, or failing build steps. All errors MUST be completely debugged and resolved FIRST before handing over work to the user.
17. **Post-Execution Git Verification Prompt**: Upon verifying that all code builds cleanly and unit tests pass with ZERO errors, the AI Agent MUST explicitly ask the user:
    > *"Does the generated/modified code pass your checks? Would you like me to perform a `git add`, `git commit`, and `git push` now?"*

---

## 9. Pre-Commit Quality Verification Checklist & Post-Task Workflow

### Checklist
Before marking any task as complete or asking for user review, verify:

- [ ] **ZERO Compilation / Runtime / Test Errors**: Code compiles cleanly without build, syntax, or unit test errors.
- [ ] C# file path aligns with namespace structure (`Game.Bootstrap`, `Game.Core`, `Game.Features.*`, etc.).
- [ ] Dependencies are properly bound in VContainer `LifetimeScope`.
- [ ] Logic systems are pure C# without direct `MonoBehaviour` references.
- [ ] UI relies on Reactive Data Binding / Events rather than `Update()` polling.
- [ ] Text strings use `ILocalizationService` keys without hardcoding.
- [ ] Temporary objects use `IGameObjectPool` without direct `Instantiate()` calls.
- [ ] Logging uses `ILoggerService` without raw `Debug.Log()` calls.
- [ ] Save models follow strict boundary rules (Character vs. World vs. Runtime) with proper schema versioning and migration steps.
- [ ] Addressable handles are registered for release on scope disposal.
- [ ] Async methods return `UniTask` or `UniTask<T>`.
- [ ] All persistent structs/classes carry explicit `[MessagePackObject]` and `[Key(n)]` attributes.
- [ ] Unit tests are written and passing for all core logic, formulas, and data compiler scripts.

### Mandatory Post-Task Action
After code updates are completely debugged, verified, and free of errors:
1. Summarize the changes made.
2. Prompt the user: **"Does the code pass? Should I proceed to commit & push to Git now?"**
3. Execute `git commit` and `git push` ONLY after receiving explicit user approval.
