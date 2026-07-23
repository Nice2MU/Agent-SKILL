---
name: unity-rpg-world-system
description: Standards and data structures for World Persistence, Chunks, Spawn System, Buildings, Farming, Day/Night, Weather, Guilds, Parties, and Trade in Unity 6 RPG.
---

# Unity 6 World & Social Systems Skill Standard

This skill governs **World State Persistence**, **Chunk Management**, **Building & Farming Structures**, and **Social Systems (Guild, Party, Trade)**.

---

## 1. World Persistent Data Blueprint

```csharp
namespace Game.Persistence.WorldSave
{
    using System.Collections.Generic;
    using MessagePack;

    [MessagePackObject]
    public class WorldSaveData
    {
        [Key(0)] public int Version { get; set; } = 1;
        [Key(1)] public string WorldId { get; set; }
        [Key(2)] public int WorldSeed { get; set; }
        [Key(3)] public double InGameTime { get; set; }
        [Key(4)] public byte WeatherState { get; set; }

        [Key(5)] public List<BuildingSaveData> Buildings { get; set; } = new();
        [Key(6)] public List<FarmPlotSaveData> FarmPlots { get; set; } = new();
        [Key(7)] public List<GuildSaveData> Guilds { get; set; } = new();
        [Key(8)] public List<PartySaveData> Parties { get; set; } = new();
    }
}
```
