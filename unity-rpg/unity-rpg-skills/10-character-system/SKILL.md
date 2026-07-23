---
name: unity-rpg-character-system
description: Character entity architecture, Stats, Base/Job Levels, Inventory, Equipment, Card Socketing, Refinement, Enchanting, and Costume components in Unity 6 RPG.
---

# Unity 6 Character System Skill Standard

This skill defines the technical standards for **Character Entities**, **Job Progression**, **Stats**, **Inventory Management**, **Equipment**, **Card Sockets**, **Refinement**, and **Enchants**.

---

## 1. Equipment & Card Data Blueprint

```csharp
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

```csharp
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

        // Cross-World ID References
        [Key(6)] public string WorldIdReference { get; set; }
        [Key(7)] public string GuildIdReference { get; set; }
        [Key(8)] public string PartyIdReference { get; set; }
    }
}
```
