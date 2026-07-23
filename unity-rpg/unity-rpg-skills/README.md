# Unity 6 Modular Data-Driven RPG Architecture Skill Suite

This directory contains the definitive **11-Module Skill Suite** for building enterprise Unity 6 RPG applications using **VContainer**, **MessagePack**, **Addressables**, and **Server-Authoritative Netcode**.

---

## 📁 Skill Suite Overview (11 Specialized Modules)

| File / Directory | Scope & Technical Domain |
| :--- | :--- |
| **`SKILL.md`** | Master All-in-One skill specification and architecture reference. |
| **`00-master-architecture/`** | Master project directory tree, namespace mappings, VContainer scope hierarchy, global rules. |
| **`01-runtime-framework/`** | GameObjectPool, ObjectFactory, UpdateLoop, TickSystem, TimeProvider, Core Services. |
| **`02-netcode-prediction/`** | Server-Authoritative co-op topology, Tick synchronization engine, Input ring buffer, Client prediction & reconciliation. |
| **`03-persistence-save/`** | MessagePack binary serialization, Character vs World save boundaries, Atomic transaction protocol, Schema migration. |
| **`04-data-pipeline/`** | ScriptableObject + CSV data compiler pipeline, Immutable Definitions vs Runtime Instances, Addressables asset management. |
| **`05-ui-framework/`** | Addressables UI panel loading, Presenter/ViewModel patterns, EventBus UI bindings, Responsive layout guidelines. |
| **`06-coding-standards/`** | C# 12 conventions, Zero-allocation hot path rules, UniTask async patterns, Thread safety, Naming rules. |
| **`07-feature-generator/`** | Step-by-step Meta-Blueprint for AI Agents to scaffold new features (e.g. Quests, Crafting, Mercenaries). |
| **`08-combat-system/`** | Hybrid ECS Combat Engine, Damage Formula calculation, Element/Race/Size matrices, Buff & Status Effects. |
| **`09-world-system/`** | World persistent state, Terrain Chunks, Buildings, Farming, Guilds (Info/Members/Storage/Skills), Parties, Trade. |
| **`10-character-system/`** | Character entity components, Job levels, Stats, Inventory, Equipment, Card sockets, Refinement, Enchants, Costumes. |
