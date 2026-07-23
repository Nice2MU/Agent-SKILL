---
name: unity-rpg-feature-generator
description: Step-by-step meta-blueprint and checklist for AI Agents to scaffold new game features (e.g. Quest, Craft, Mercenary, Achievement) in Unity 6 RPG.
---

# Unity 6 Feature Generator Skill Template

This skill serves as the **Standard Blueprint** when an AI Agent or Developer is tasked with generating or implementing a **New Feature** (e.g. `Quest`, `Craft`, `Mercenary`, `Achievement`).

---

## 1. Feature Directory Layout

```
Assets/Scripts/Features/{FeatureName}/
├── Runtime/             --> Pure data structs, ECS components & Tick systems
│   ├── {FeatureName}Entity.cs
│   ├── {FeatureName}State.cs
│   └── {FeatureName}System.cs
├── Definition/          --> ScriptableObject or CSV definitions
│   └── {FeatureName}Definition.cs
├── Save/                --> MessagePack save structures
│   └── {FeatureName}SaveData.cs
└── UI/                  --> UI View, Presenter, ViewModel
    ├── {FeatureName}View.cs
    └── {FeatureName}Presenter.cs
```
