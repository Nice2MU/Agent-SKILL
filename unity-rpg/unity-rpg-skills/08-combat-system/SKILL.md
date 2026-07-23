---
name: unity-rpg-combat-system
description: Dedicated skill for Hybrid ECS Combat Engine, Damage Formula calculation, Element/Race/Size matrices, Buff & Status Effects in Unity 6 RPG.
---

# Unity 6 Combat System Skill Standard

This skill governs the **Combat Engine**, **Damage Calculation Formulas**, **Element/Race/Size Matrices**, **Skill Execution**, and **Buff / Status Effect Systems**.

---

## 1. Combat Data & Formula System

```csharp
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
        public readonly bool IsDead => CurrentHp <= 0;
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

    public static class FormulaSystem
    {
        public static float GetElementModifier(ElementType attack, ElementType defense)
        {
            if (attack == ElementType.Fire && defense == ElementType.Earth) return 1.5f;
            if (attack == ElementType.Water && defense == ElementType.Fire) return 1.5f;
            return 1.0f;
        }

        public static int CalculateDamage(in DamageInstance dmg, float elementMultiplier, float sizeModifier, int targetDefense)
        {
            float baseDamage = Math.Max(1, dmg.RawDamage - targetDefense);
            float finalDamage = baseDamage * elementMultiplier * sizeModifier;
            if (dmg.IsCritical) finalDamage *= 1.4f;
            return Math.Max(1, (int)finalDamage);
        }
    }
}
```
