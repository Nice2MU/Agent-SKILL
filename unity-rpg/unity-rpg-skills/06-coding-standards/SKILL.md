---
name: unity-rpg-coding-standards
description: Production C# 12 / Unity 6 coding standards, zero-allocation hot path rules, UniTask async patterns, thread safety, and naming conventions.
---

# Unity 6 C# Coding Standards & Guidelines

This skill defines mandatory C# coding standards, performance rules, and async conventions.

---

## 1. Zero-Allocation Hot Path Rules

1. **Pass Structs by `in` Reference**: Use `in` for large read-only structs passed to methods (`ApplyDamage(in DamageInstance dmg)`).
2. **No LINQ in Tick / Update**: Never use `.Where()`, `.Select()`, `.ToList()`, or `foreach` over `IEnumerable` inside `Tick()` or `Update()`.
3. **Use Struct Events**: All events published to `IEventBus` must be value type structs (`struct`).
