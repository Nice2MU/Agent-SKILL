---
name: unity-rpg-persistence-save
description: Standards, data boundaries, atomic transaction protocols, MessagePack serialization, and schema migration blueprints for Unity 6 RPG save system.
---

# Unity 6 Persistence & Save System Skill Standard

This skill governs **Save Data Boundaries**, **MessagePack Serialization**, **Atomic Save Transactions**, and **Schema Migration Protocols** for Unity 6.

---

## 1. Save Data Hierarchy

```
Save/
├── Characters/             (Knight.dat, Wizard.dat)
├── Worlds/                 (MyWorld.dat, Sandbox.dat)
├── Metadata.json           (Fast UI List Display)
├── Backups/                (Rolling Timestamped Backups)
└── Settings.json           (Graphics, Input, Audio)
```

---

## 2. Save Boundary Matrix

| Category | Character Save (`.dat`) | World Save (`.dat`) | Runtime Excluded |
| :--- | :---: | :---: | :---: |
| **Base/Job Level & Stats** | ✅ Included | ❌ Excluded | ❌ |
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

---

## 3. Atomic Transaction Protocol

1. **Temp Write**: Serialize payload to target path + `.tmp`.
2. **Validate**: Verify file non-zero byte length and MessagePack CRC checksum.
3. **Commit**: Replace active `.dat` file with `.tmp` atomically.
4. **Rollback**: On failure, purge `.tmp`, restore backup, throw `SaveTransactionException`.
