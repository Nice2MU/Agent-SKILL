---
name: unity-rpg-netcode-prediction
description: Technical specs, workflows, and implementation blueprints for Server-Authoritative Netcode, Fixed Tick Synchronization, Client Prediction, Input Buffering, and Reconciliation in Unity 6 RPG.
---

# Unity 6 Netcode, Client Prediction & Tick Engine Skill Standard

This skill defines the technical standards for **Host/Client Co-op Networking**, **Fixed Tick Synchronization**, **Client-Side Prediction**, **Input Buffering**, and **Server Reconciliation**.

---

## 1. Network Topology & Authority Rules

1. **Topology**: Single Player + Host/Client Co-op over UDP.
2. **Server Authority**:
   - Host runs full authoritative simulation logic.
   - Clients send raw uncompressed input commands (`CharacterInputCmd`) per tick.
   - Clients **NEVER** dictate health changes, inventory updates, or item drops.
3. **Tick Sync**:
   - Simulation tick rate is fixed (e.g. 30 Hz = 33.33ms or 60 Hz = 16.66ms).
   - Render frame rate (`Time.deltaTime`) is fully decoupled from network tick rate (`NetworkTime.Tick`).

---

## 2. Netcode Input Buffer Blueprint

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
