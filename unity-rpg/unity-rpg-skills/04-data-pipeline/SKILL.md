---
name: unity-rpg-data-pipeline
description: Specification and blueprints for ScriptableObject + CSV data pipelines, Editor binary compilers, Addressables asset management, and Definition vs Instance patterns in Unity 6 RPG.
---

# Unity 6 Data Pipeline & Addressables Skill Standard

This skill establishes standards for **Designer-Authored Data (CSV/ScriptableObject)**, **Binary Data Compilers**, **Addressable Asset Pipelines**, and the **Definition vs Runtime Instance** pattern.

---

## 1. Definition vs Runtime Instance

- **Definition**: Static immutable designer data (`ItemDefinition`, `MonsterDefinition`). Stored in CSV/ScriptableObject and compiled to binary lookup tables.
- **Runtime Instance**: Dynamic state in memory (`ItemInstance`, `CharacterEntity`). References Definition ID + stores mutable state (durability, refinement, socketed cards).

---

## 2. CSV Compiler Blueprint

```csharp
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
