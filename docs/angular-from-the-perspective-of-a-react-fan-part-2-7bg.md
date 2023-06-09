# 从反作用风扇的角度看有角度-第 2 部分

> 原文：<https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-2-7bg>

### 角级数

*   [第 1 部分-引导和类型脚本](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-1-188p)
*   [**第二部分——测试**](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-2-7bg)
*   [第 3 部分-状态管理](https://dev.to/sirech/angular-from-the-perspective-of-a-react-fan-part-3-5dnj)

本系列的第一部分是关于设置*角度*的第一步。在这篇文章中，我想谈谈测试。这是迄今为止最让我失望的部分。与类似 [Jest](https://facebook.github.io/jest/) 的东西相比，设置测试似乎没有那么简单。为 *Angular* 编写新的测试让我想起了我们使用 [QUnit](http://qunitjs.com/) 的一个老项目，在那里编写 JS 测试是每个人都积极试图避免的事情。

## 测试的重要性

最近，我一直在努力把更多的注意力放在 TDD T1 上。一般来说，这是一个很好的实践。当你在使用一项不熟悉的技术时，把额外的注意力放在测试上可以给你更多的信心去改变事情。

如果你想做任何像样的 CI/CD，除了有一套好的测试之外，没有其他方法，这些测试容易维护和扩展，运行可靠快速。框架在确保这一点上起着很大的作用，或者至少它应该让路，以便您可以在它的基础上构建您需要的东西。

## 速度

快速测试不是奢侈品，而是快速反馈循环的关键要求。反过来，这使你能够在小的变化中快速前进，始终处于工作状态。这里是 *Angular* 掉球最多的地方。这个提示已经成为我最近生活的一大部分:

```
npm t
01 05 2018 00:39:58.283:INFO [karma]: Karma v2.0.0 server started at http://0.0.0.0:9876/
01 05 2018 00:39:58.285:INFO [launcher]: Launching browser ChromeHeadless with unlimited concurrency
01 05 2018 00:39:58.372:INFO [launcher]: Starting browser ChromeHeadless
01 05 2018 00:40:29.587:INFO [HeadlessChrome 67.0.3391 (Mac OS X 10.13.4)]: Connected on socket jC8ho-ctNv4E4irCAAAA with id 26948242 
```

Enter fullscreen mode Exit fullscreen mode

出于我不太理解的原因，即使运行简单的单元测试也需要启动一个 *Headless Chrome* 的实例。来自*的笑话*，不得不等两分钟来运行一个单独的测试让我发疯。理论上有一个观察模式，但是我被告知，至少在我们的项目中，它在拾取变化方面是不可靠的。

说到运行单个测试，我发现运行单个测试的唯一方法是使用`fit`或`fdescribe`块，这相当不方便。[似乎没有简单的方法来实现这一点，所以我可能要再试一次手表模式。现在，我们必须配置](https://stackoverflow.com/a/43669082/3785) [tslint](https://palantir.github.io/tslint/) ，这样当您意外使用`fit`或`fdescribe`提交测试时，它就会失败。否则，您可能会错误地禁用大多数测试套件。

无论如何，这是一个相当大的缺点。不得不一遍又一遍地等待您的简单测试，这完全让我脱离了流程，并且很容易走捷径，一次构建许多更改，而不是分别测试它们。我真的希望这只是我用错了工具，并且在我最意想不到的时候得到启示。我的信仰至今没有得到回报。

## 单元测试

我所写的单元测试主要有两种类型:服务测试和组件测试。服务相当简单，因为它们往往是易于测试的纯功能。用 Jasmine 可以相当容易地模仿远程调用。

组件需要自己的部分。

### 测试组件

当开始着手一个`Angular`项目时，我的第一反应是将较大的组件分解成多个小的[表示组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)。然而，为他们编写一些简单的单元测试被证明是相当具有挑战性的。

当测试不管理任何状态的简单小组件时，我经常发现做一个健全性检查(组件可以无错误地呈现)或者一个简单的快照测试来浏览 html 就足够了。在*反应* :
中可能是这样的

```
import React from 'react'
import { shallow } from 'enzyme'
import Techs from './Techs'

describe('components', () => {
  describe('Techs', () => {
    it('renders correctly', () => {
      const component = shallow(<Techs />)
      expect(component).toHaveLength(1)
    })
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我想在 *Angular* 中做一件类似的事情，但是我费了很大的劲才在不写很多代码的情况下达到同样的效果。谢天谢地，我的一个同事[已经写了这个](https://medium.com/@AikoPath/testing-angular-components-with-input-3bd6c07cfaf6)。据我所知，这是最小的健全性检查测试，你可以得到一个*角*组件:

```
import {Component} from '@angular/core';
import {ComponentFixture, TestBed} from '@angular/core/testing';
import {MyComponent} from './my-component.component';

describe('MyComponent', () => {
  @Component({
    selector: `host-component`,
    template: `
      <my-component [count]="count"></my-component>`
  })
  class TestHostComponent {
    count = 3
  }

  let testHostFixture: ComponentFixture<TestHostComponent>;

  beforeEach(async () => {
    TestBed.configureTestingModule({
      declarations: [MyComponent, TestHostComponent]
    })
      .compileComponents();
  });

  beforeEach(() => {
    testHostFixture = TestBed.createComponent(TestHostComponent);
    testHostFixture.detectChanges();
  });

  it('should create', () => {
    expect(testHostFixture.componentInstance).toBeTruthy();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

仅仅渲染一个简单的组件就要花费大量的精力！您需要创建一个模拟组件来包装您想要测试的组件，并用`TestBed`进行一系列初始化。此外，随着组件的增长，这似乎变得越来越复杂。与[酵素](https://airbnb.io/enzyme/)相比，这感觉相当重量级。同样，你可以测试任何你想测试的东西。但是你增加的摩擦越多，走捷径的机会就越大。

另一个让我困惑的地方是在测试中什么时候使用`detectChanges`和`whenStable`。我见过在不同的组合中使用它们的测试。如果你没有使用正确的顺序，它们很容易断裂。

## 端到端测试

假设您已经在使用浏览器进行单元测试，那么与其他框架相比，从概念上跳到完整的端到端测试要小一些。*角度*使用[量角器](https://www.protractortest.org/#/)，量角器位于[硒](https://www.seleniumhq.org/)的顶部。在完全意料之中的消息中，他们遭受了所有其他 e2e 框架所遭受的同样的责难。确保你在正确的水平上测试可以让你省去很多痛苦。当一个单元测试已经足够时，不要写一个 e2e 测试！

## 正确的等级测试

对此还有什么可说的？只需阅读[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)。这让我想起了我过去关于自动化测试需求的讨论。幸运的是，这场战争似乎已经结束。在不同的集成层次上编写测试，而不是仅仅做超高层次的测试，现在已经取而代之了。

## 接下来

到目前为止，测试还相当粗糙。在一个架构糟糕的应用上工作没有任何帮助，但是仍然比你想象的更痛苦。另一个对我来说非常重要的领域，来自 *React* ，是状态管理。我将在下一篇文章中谈到这一点。