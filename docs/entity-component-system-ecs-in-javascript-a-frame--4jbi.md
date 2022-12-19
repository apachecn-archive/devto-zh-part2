# JavaScript A-Frame 中的实体-组件-系统

> 原文：<https://dev.to/pancy/entity-component-system-ecs-in-javascript-a-frame--4jbi>

[![ECS](../Images/781fa7d2289e07f070e78b1c704209b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M6vS1_E9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A8bx8gktS7trsjiwp.png)

我一直在用 Mozilla 的开源 WebVR 框架 [A-Frame](https://aframe.io/) 进行开发。使 A-Frame 与 Three.js 等 WebGL 库非常不同的是——首先，它构建在 Three.js 之上，通过 HTML DOM 和属性更具声明性，其次，它迎合了 WebVR，这意味着它自带了 VR 模式，最后也是最重要的，它采用了[实体-组件-系统(ECS)](https://en.wikipedia.org/wiki/Entity%E2%80%93component%E2%80%93system) 模式。

我没有参与过游戏设计，因此这是我第一次接触到 ECS。简而言之，作为一种通用的设计模式，它与组合非常相似。抽象层次上的区别在于，组合侧重于多态中的“HAS-A ”,而 ECS 侧重于将行为应用于实体。

然而，在面向对象模式(OOP)中，子类的任何实例都是其超类的实例。这种达尔文式的抽象模型几十年来一直非常有用，直到最近才成为科技行业的一个伟大的多态模型。不知何故，我们发现，从你的祖先那里继承包袱并不总是最理想的，也不一定容易理解。此外，多重继承(子类化多个超类)几乎总是一团糟，因为它突然为一个类继承多个父类的子实例提出了一个存在性问题。

另一方面，接口是一个很好的抽象。它确实暗示了一种轻微的“是-是”关系，但是谢天谢地，它没有让其他对象继承它，或者对它们来说表现得像一个超级存在。它只不过是一个松散的看门人，只要它有正确的属性或方法集，就让任何实例通过大门，成为俱乐部的成员。因此，这意味着一个实例可以属于它被允许做的任何“俱乐部”,只要当它在那里时，它可以做其他人做的事情并融入其中。不知何故，作为一名程序员，您仍然需要用这种灵活性来权衡维护接口代码的新复杂性。还有，当你从另一个角度看它时，它会感觉像是一种浅层次的继承，没有超越一个层次。

回到 ECS。在游戏设计开发圈里还是挺有知名度的。想象一下街头霸王风格的战斗游戏，你选择一个角色与对方对战。该实体是一个人类(或非人类)战士的空骨架，可能拥有一系列简单的行为，如拳打、脚踢或跳跃，以及一个属性，如 HP(生命值)。您选择的角色实际上是一组预定义的组件/行为，可以应用并增强实体。例如，一个春丽组件可以修改一个基础实体的跳跃行为，使之成为春丽独有的，添加旋转鸟踢腿动作，当然，还可以应用角色的纹理。这些组件也可以与其他组件相互作用，例如，旋转鸟踢可以产生-5 HP 的默认伤害，但是当与一个有精神破碎者组件的实体相互作用时，它可以产生-1 HP 的伤害。

ECS 中的 S，即系统，在 A-Frame 中提到的不多。它被称为一个可选的服务层，集中了所有注册组件的持久状态和控制，很像 Angular 中的服务。我目前使用 A-Frame 系统与 Angular UI 组件进行通信，以尽可能隔离两个框架。

ECS 是一种非常灵活的模式，我明白为什么它适合游戏开发。它关注的是解耦和可用性，而不是试图实现其他设计模式所追求的书生气的抽象。

例如，这里有一个 A-Frame 中的 a-box 基本实体，它基本上在画布上呈现了一个 3D 立方体:

```
 <a-box color="#FFF" position="0 0 0"></a-box> 
```

Enter fullscreen mode Exit fullscreen mode

为了让 a-box 发出信号(实际上，控制台记录一条消息)，你可以注册一个`sing`组件，就像这样:

```
 AFRAME.registerComponent('sing', {
  schema: {type: 'string', default: "doh re me!"},
  init: function() {
    console.log(this.data);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

并使`a-box`采用该组件:

```
 <a-box sing="helloooo" color="#FFF" position="0 0 0"></a-box> 
```

Enter fullscreen mode Exit fullscreen mode

然后在加载时，你会看到浏览器上的小日志“helloooo”。

一个组件有一个特定的生命周期钩子，允许我们控制它执行行为的时间。让我们进入更新周期，唱大声一点:

```
 AFRAME.registerComponent('sing', {
  schema: {type: 'string', default: "doh re me!"},
  init: function() {
    console.log(this.data);
  }
  // update 
  update: function() {
    alert(this.data);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，尝试将`sing`属性设置为其他值，也许什么都不是:

```
 let box = document.querySelector('a-box');
box.setAttribute('sing', ''); 
```

Enter fullscreen mode Exit fullscreen mode

窗口弹出一个警告，默认字符串为“doh re me！”。

这种实体-组件关系非常灵活和强大。想象你可以添加和删除元素，改变颜色，位置，移动等。基于 DOM 事件。我们甚至还没有涉足网络虚拟现实的领域，但这种模式已经根深蒂固。

原发表[此处](https://medium.com/@jochasinga/entity-component-system-ecs-in-a-frame-and-javascript-f5b7cdba7248)。