---
name: unity-rpg-ui-framework
description: Standards, component patterns, Addressables UI loading, and EventBus binding workflows for Unity 6 RPG UI system.
---

# Unity 6 UI System Framework Skill Standard

This skill governs **UI Architecture**, **Presenter / ViewModel Patterns**, **Addressable Panel Loading**, and **EventBus UI Binding**.

---

## 1. Presenter & View Pattern

```csharp
namespace Game.UI.Core
{
    using UnityEngine;
    using Cysharp.Threading.Tasks;

    public abstract class BaseUIView : MonoBehaviour
    {
        [SerializeField] private CanvasGroup canvasGroup;

        public virtual UniTask OpenAsync()
        {
            gameObject.SetActive(true);
            if (canvasGroup != null)
            {
                canvasGroup.alpha = 1.0f;
                canvasGroup.blocksRaycasts = true;
            }
            return UniTask.CompletedTask;
        }

        public virtual UniTask CloseAsync()
        {
            if (canvasGroup != null)
            {
                canvasGroup.alpha = 0.0f;
                canvasGroup.blocksRaycasts = false;
            }
            gameObject.SetActive(false);
            return UniTask.CompletedTask;
        }
    }
}
```
