# Unity C#中的简单 UI 元素拖动脚本

> 原文：<https://dev.to/matthewodle/simple-ui-element-dragging-script-in-unity-c-450p>

将这个脚本附加到你的游戏对象/预设上，为你的 UI 元素启用一个简单的拖动机制。你还需要添加`EventSystem`物体到你的场景中。

这相当简单。被覆盖的`UnityEngine.EventSystems.OnPointerDown`和`OnPointerUp`方法只是切换拖动`bool`。然后`Update`方法将修改这个脚本所附着的对象的位置。

使用这种解决方案，UI 元素的中心将与光标对齐，但是为了使其更清晰，可以根据触发`OnPointerDown`时 UI 元素内的位置添加一个偏移量。

```
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

public class UIElementDragger : EventTrigger {

    private bool dragging;

    public void Update() {
        if (dragging) {
            transform.position = new Vector2(Input.mousePosition.x, Input.mousePosition.y);
        }
    }

    public override void OnPointerDown(PointerEventData eventData) {
        dragging = true;
    }

    public override void OnPointerUp(PointerEventData eventData) {
        dragging = false;
    }
} 
```