# 如何为您的 web 应用程序创建自定义上下文菜单

> 原文：<https://dev.to/iamafro/how-to-create-a-custom-context-menu--5d7p>

在我们开始之前，这里有一个我们将要创建的预览。

[![Context menu preview](img/856b56689279d84f278cac9c2070d9fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3E-EBwbM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://res.cloudinary.com/hackafro/image/upload/v1518521490/ezgif.com-video-to-gif_gxie8f.gif)

##### 我们来讨论几个自定义上下文菜单的用例。

1.  您希望在 web 应用程序的某个区域显示自定义上下文菜单，而不是默认菜单。

我已经穷尽了我的用例示例，但是我确信你可以想出一些其他的

#### 咱们码！

首先，我们将使用 HTML 创建一个表示自定义上下文菜单的项目列表。

```
<div class="menu">
  <ul class="menu-options">
    <li class="menu-option">Back</li>
    <li class="menu-option">Reload</li>
    <li class="menu-option">Save</li>
    <li class="menu-option">Save As</li>
    <li class="menu-option">Inspect</li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将添加几行 scss 来更新我们的自定义菜单。让它看起来又漂亮又干净。

```
.menu {
  width: 120px;
  box-shadow: 0 4px 5px 3px rgba(0, 0, 0, 0.2);
  position: relative;
  display: none;

  .menu-options {
    list-style: none;
    padding: 10px 0;

    .menu-option {
      font-weight: 500;
      font-size: 14px;
      padding: 10px 40px 10px 20px;
      cursor: pointer;

      &:hover {
        background: rgba(0, 0, 0, 0.2);
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 到主菜

每当我们右击查看页面上的上下文菜单时，就会触发一个事件。事件名称是(你猜对了)`contextmenu`。疯狂吧？没有那么多。
我们要做的第一件事是监听这个事件，然后阻止默认行为的触发。

```
window.addEventListener("contextmenu", e => {
  e.preventDefault();
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在整个窗口上监听一个`contextmenu`事件，但是你总是可以指定一个特定的区域而不是整个窗口。

现在我们已经弄清楚了，我们将在事件回调中设置自定义上下文菜单的显示。我们将利用事件对象上的两个属性来决定在哪里显示我们的菜单。这将是用户点击的源头。

[![Event screenshoot](img/772d5d234701b330549dc90409219eac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ysk5hkXV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hackafro/image/upload/v1518517893/Capture_hhi0vg.png)

事件回调的`pageX`和`pageY`属性描述了当上下文菜单请求被触发时鼠标的位置。您需要将上下文菜单放在那个位置。让我们更新我们的 js 代码来反映这些变化。

```
const toggleMenu = command => {
  menu.style.display = command === "show" ? "block" : "none";
};

const setPosition = ({ top, left }) => {
  menu.style.left = `${left}px`;
  menu.style.top = `${top}px`;
  toggleMenu('show');
};

window.addEventListener("contextmenu", e => {
  e.preventDefault();
  const origin = {
    left: e.pageX,
    top: e.pageY
  };
  setPosition(origin);
  return false;
}); 
```

Enter fullscreen mode Exit fullscreen mode

`toggleMenu`功能切换菜单的显示，`setPosition`功能设置菜单的位置。函数名是不言自明的。

到目前为止，我们应该有一个全功能的上下文菜单，但问题是我们的菜单没有消失，它就在那里。因此，如果上下文菜单可见，我们必须设置另一个事件监听器来隐藏它。为了做到这一点，我们将在您设置了自定义上下文菜单的窗口或区域中监听一个`click`事件。

```
const menu = document.querySelector(".menu");
let menuVisible = false;

const toggleMenu = command => {
  menu.style.display = command === "show" ? "block" : "none";
  menuVisible = !menuVisible;
};

const setPosition = ({ top, left }) => {
  menu.style.left = `${left}px`;
  menu.style.top = `${top}px`;
  toggleMenu("show");
};

window.addEventListener("click", e => {
  if(menuVisible)toggleMenu("hide");
});

window.addEventListener("contextmenu", e => {
  e.preventDefault();
  const origin = {
    left: e.pageX,
    top: e.pageY
  };
  setPosition(origin);
  return false;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的 js 文件的最终外观。我们有一个工作上下文菜单，但像往常一样，我们仍然需要一些改进，使我们的上下文菜单真正优雅。

1.  当点击非常接近窗口的底部或顶部时，如何定位菜单。
2.  添加一些动画来平滑菜单的进入和退出。

只需要几行代码就可以实现这一点。我相信你能想出来。查看 [codepen](https://codepen.io/Iamafro/pen/bLqyGd) 获得完整的代码库。如有任何建议和问题，请在推特上联系我