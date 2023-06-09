# Unity 中简单的 3D 摄像机移动

> 原文：<https://dev.to/matthewodle/simple-3d-camera-movement-1lcj>

我正在制作一个 3D 村庄管理 sim，当事情变得疯狂时，我希望能够放大特定区域的行动。

这个相机控制脚本寻找水平和垂直轴输入(WASD 和箭头键)以允许玩家平移。它还监视鼠标滚轮，让玩家可以升高和降低摄像机。

只要把它放到场景中的游戏对象上，你就可以开始了。

基本模式是:

*   if 输入
    *   使用目标输入创建一个新的`Vector3(x, y, z)`
        *   `x`和`z`为平移，`y`为缩放，其余`0`
    *   将`Vector3`乘以某个预定义的速度
    *   将新的`Vector3`添加到摄像机变换的位置

我反转了鼠标滚轮输入，这样向上滚动可以放大，向下滚动可以缩小。

```
using UnityEngine;

public class CameraController : MonoBehaviour {

    private float moveSpeed = 0.5f;
    private float scrollSpeed = 10f;

    void Update () {
        if (Input.GetAxisRaw("Horizontal") != 0 || Input.GetAxisRaw("Vertical") != 0) {
            transform.position += moveSpeed * new Vector3(Input.GetAxisRaw("Horizontal"), 0, Input.GetAxisRaw("Vertical"));
        }

        if (Input.GetAxis("Mouse ScrollWheel") != 0) {
            transform.position += scrollSpeed * new Vector3(0, -Input.GetAxis("Mouse ScrollWheel"), 0);
        }
    }

} 
```