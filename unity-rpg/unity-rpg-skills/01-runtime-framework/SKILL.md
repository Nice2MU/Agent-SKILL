---
name: unity-rpg-runtime-framework
description: Standards and code blueprints for Core Framework utilities, GameObject Pooling, ObjectFactory, TickSystem, UpdateLoop, and TimeProvider in Unity 6 RPG.
---

# Unity 6 Runtime Framework Skill Standard

This skill defines the core runtime infrastructure components under `Game.Core.Framework`.

---

## 1. Core Framework Components

1. **GameObjectPool**: Generic, zero-allocation object pooling for visual views and particles.
2. **ObjectFactory**: VContainer-integrated factory for spawning entities and instantiating prefabs with dependency injection.
3. **TickSystem**: Centralized tick dispatcher operating at fixed intervals (30Hz / 60Hz).
4. **TimeProvider**: Abstraction for server time, world simulation time, and paused state.

---

## 2. GameObject Pool Blueprint

```csharp
// File: Assets/Scripts/Core/Framework/GameObjectPool.cs
namespace Game.Core.Framework
{
    using System.Collections.Generic;
    using UnityEngine;

    public class GameObjectPool
    {
        private readonly GameObject _prefab;
        private readonly Queue<GameObject> _pool = new();
        private readonly Transform _parent;

        public GameObjectPool(GameObject prefab, int initialCapacity, Transform parent = null)
        {
            _prefab = prefab;
            _parent = parent;

            for (int i = 0; i < initialCapacity; i++)
            {
                var instance = Object.Instantiate(_prefab, _parent);
                instance.SetActive(false);
                _pool.Enqueue(instance);
            }
        }

        public GameObject Spawn(Vector3 position, Quaternion rotation)
        {
            var obj = _pool.Count > 0 ? _pool.Dequeue() : Object.Instantiate(_prefab, _parent);
            obj.transform.SetPositionAndRotation(position, rotation);
            obj.SetActive(true);
            return obj;
        }

        public void Despawn(GameObject obj)
        {
            obj.SetActive(false);
            _pool.Enqueue(obj);
        }
    }
}
```
