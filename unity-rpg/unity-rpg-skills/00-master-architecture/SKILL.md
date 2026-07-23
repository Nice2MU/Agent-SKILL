---
name: unity-rpg-master-architecture
description: Master architectural guide, project directory tree, namespace mappings, VContainer dependency injection hierarchy, and foundational design rules for Unity 6 RPG.
---

# Unity 6 RPG Master Architecture Standard

This skill defines the overarching architecture, folder layout, namespace standards, and dependency injection hierarchies for building an enterprise-grade Modular Data-Driven RPG in **Unity 6**.

---

## 1. Core Architecture Matrix

| Layer | Responsibility | Technology / Pattern |
| :--- | :--- | :--- |
| **Engine Core** | Unity 6 (C# 12 / .NET Standard 2.1+) | High-performance C# focus |
| **Architecture** | Modular + Data-Driven + Feature-Based | Strict feature isolation, zero cross-feature references |
| **Dependency Injection** | **VContainer** | Scoped container hierarchy (`Root` -> `World` -> `UI`) |
| **Runtime Execution** | Hybrid ECS + Component-Based | Pure C# struct components + decoupled Systems |
| **Messaging** | Type-Safe Struct EventBus | Decoupled zero-allocation Pub/Sub |
| **Asset Pipeline** | **Addressables** | Memory-managed async asset loading |
| **Serialization** | **MessagePack** | Ultra-fast binary payload serialization |
| **Save System** | Repository + Atomic Transactions | Separate World-Centric & Character-Centric files |

---

## 2. Complete Project Directory & Namespace Mapping

All source code must strictly follow this folder hierarchy and namespace mapping:

```
Assets/Scripts/
├── Bootstrap/                   --> Game.Bootstrap
├── Core/                        --> Game.Core
│   ├── Framework/               --> Game.Core.Framework
│   ├── Services/                --> Game.Core.Services
│   ├── Systems/                 --> Game.Core.Systems
│   ├── Messaging/               --> Game.Core.Messaging
│   ├── Interfaces/              --> Game.Core.Interfaces
│   ├── Utilities/               --> Game.Core.Utilities
│   ├── Extensions/              --> Game.Core.Extensions
│   └── Constants/               --> Game.Core.Constants
│
├── Features/                    --> Game.Features.[FeatureName]
│   ├── Character/               --> Game.Features.Character
│   ├── Combat/                  --> Game.Features.Combat
│   ├── Inventory/               --> Game.Features.Inventory
│   ├── World/                   --> Game.Features.World
│   ├── AI/                      --> Game.Features.AI
│   ├── Quest/                   --> Game.Features.Quest
│   ├── Craft/                   --> Game.Features.Craft
│   ├── Mercenary/               --> Game.Features.Mercenary
│   └── Achievement/             --> Game.Features.Achievement
│
├── Multiplayer/                 --> Game.Multiplayer
├── Persistence/                 --> Game.Persistence
├── UI/                         --> Game.UI
├── Data/                        --> Game.Data
├── Tools/                       --> Game.Tools (Editor / Pipeline)
└── Content/                     --> Content / Addressable Assets
```

---

## 3. VContainer Scope Hierarchy

1. **RootLifetimeScope** (Global Services & Entry Point): `AssetService`, `SaveService`, `AudioService`, `EventBus`, `NetworkService`, `Bootstrapper`.
2. **WorldLifetimeScope** (World Session): `WorldState`, `ChunkSystem`, `SpawnSystem`, `ServerTick`, `ClientPrediction`.
3. **UILifetimeScope** (UI Screens): ViewModels, Controllers, Presenters.
