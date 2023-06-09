# 教程:开发测试驱动的 angular 应用程序

> 原文：<https://dev.to/_maimart_/tutorial-developing-an-angular-app-driven-by-tests-51ae>

本文是开发测试驱动的角度应用程序(TDD)的教程。

## 简介

长期以来，复杂系统的图形用户界面都是由高级开发的面向对象编程语言的坚实而强大的技术构成的。

大多数 ui 都是胖客户端，由 WPF 或 Swing 开发。只是它们很少是作为 web 应用程序的瘦客户机。

即使在这种罕见的情况下，HTML 和 JavaScript 也会被像 ASP 或 JSF 这样的大型框架抽象出来，并呈现在服务器端。

但 JavaScript 的语言核心随着 ECMAScript 2015 发展得很好，因此对更复杂的应用程序也很感兴趣。

基于对 ECMAScript2015 的建议，TypeScript 提出并成为在非常高的语言水平上构建 web 应用程序的附加选项，甚至可以静态类型化。

这一重大的语言改进以及脸书和谷歌等巨头的大力支持，使得 web 技术越来越有兴趣开发更复杂的应用程序，如单页应用程序(SPA)。

不幸的是，在可读性、设计和测试覆盖率方面，web 应用程序的源代码仍然存在质量水平较低的不良记录。

但是，为可持续的进一步开发和维护创建高质量代码的价值是独立于技术的，确保这一价值的方法也是如此。

测试驱动开发(TDD)是极限编程(XP)的一种方法，用于开发高质量和经过良好测试的代码。

在 TDD 中，在我们开始实现它之前，我们首先在测试中指定和验证行为。当测试成功时，我们重构代码以提高代码的可读性和结构。

这导致了良好的可测试性和测试代码，以及开发人员早期必须考虑代码的结构和设计的过程。

通过首先在测试中指定功能，我们有效地只开发必需的功能，并防止开发预测性的和不必要的复杂代码。

用 Angular CLI 创建的项目，即装即用，包含了我们需要开发的由测试驱动的所有工具。

本教程基于一个简单的场景，描述了如何开发测试驱动的 Angular 项目。

## 场景

我们希望关注方法论，因此选择一个非常简单的场景。

该应用程序是一个非常轻量级的联系人管理工具，仅提供两个功能:

*   显示联系人
*   添加新联系人

## 设置角度项目

我们首先用 Angular CLI 创建一个 Angualr 项目。
`ng new ContactManager`

我们想从头开始，所以我们:

*   删除组件*应用组件*
*   清理家属 *app.module* 和【index.html】T2
*   删除目录 *e2e/src* 中的端到端规范
*   并通过使用`ng build`构建项目来检查我们的更改

## [t1【行走】](#the-walking-skeleton)

我们的系统非常小，不与任何后端通信，为了方便起见，它不是自动部署的。

所以在我们的例子中，行走骨架是开发测试驱动的应用程序的横切线。

这应该包含一个通过页面对象与 web 应用程序交互的 e2e 测试，以及由测试驱动开发的第一个小组件。

构建行走骨架的第一个合适的小故事是显示联系人。

## 接近

我们在短的 TDD 迭代中开发。

我使用了一些特殊的文本样式来使教程更加易读和清晰:

首先，我们讨论并指定我们想要在下一个 tdd
迭代中实现什么。在最好的情况下，我们不为自己开发，而是与另一个开发商
合作。

***测试失败！*** 我们指定一个相应的测试，启动它，看着它失败。

***测试成功！*** 之后，我们实现功能并再次开始测试，直到成功。

我们检查我们的代码风格，做一些重构，然后我们开始下一个小的 tdd 迭代。

## 在第一次 e2e 测试中指定故事

我们在新的 e2e 规范/e2e/src/c _ contact-management . e2e-spec . ts _ 中规定了“联系管理”的上下文。

*我们定义，联系人管理最初列出了两个联系人“Max”和“Moritz”。*

在 e2e 测试中，我们希望在很高的层次上与应用程序进行交互，并将对 HTML 元素的访问委托给所谓的“页面对象”。

在 TDD 方式中，我们编写 e2e 测试，就好像已经存在一个 class _ ContactManagementPage _ 并从客户的角度以最有意义的方式定义它。

我们创建一个 *ContactManagementPage* 的实例，导航到它的视图并验证联系人“Max”和“Moritz”是否被列出。

```
describe('Contact Management', function () {

  it('should show a list of contacts, initially containing "Max" and "Moritz"', function () {
    const page: ContactManagementPage = new ContactManagementPage();
    page.navigateTo();
    expect(page.shownNamesOfContacts()).toContain('Max');
    expect(page.shownNamesOfContacts()).toContain('Moritz');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们创建类*ContactManagementPage**/e2e/src/page objects/contact-management . po . ts，*，如 e2e 测试中所假设的。

***测试失败！*** 现在测试编译通过，但不出所料失败。

我们开始实现页面对象的*navigate to*-方法，该方法导航到联系人管理视图。在我们的例子中，它是根内容“/”。

接下来我们需要所有列出的联系人的名字。此时，我们定义视图包含类*“contact”、*的 HTML 元素，这些元素又包含类*“name”*的元素。

我们通过获取这些元素并映射它们的文本值来实现 *#shownNamesOfContacts* 。

量角器与其自己的 *Promise* 类异步工作。我们必须确保导入和使用正确的 *Promise* 类的量角器库。

```
import {browser, by, element, ElementArrayFinder, ElementFinder, promise} from 'protractor';
import Promise = promise.Promise;

export class ContactManagementPage {
  navigateTo() {
    browser.get('/');
  }

  shownNamesOfContacts(): Promise<string[]> {
    return this.contactNameItems().map(contactNameItem => contactNameItem.getText());
  }

  private contactNameItems(): ElementArrayFinder {
    return element.all(by.css('.contact .name'));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

***测试失败！*** 测试失败，提示找不到角度应用。目前没有角分量得到自举。

为了解决这个问题，我们接下来用 Angular CLI 创建一个 Angular 组件“*联系人列表*”。

`ng generate component contact-list`

它被自动分配给全局 a_pp-module_ 并在其中声明。到目前为止，这对于本教程来说还可以，但是我们还必须将组件集成到我们的*index.html*中，并配置我们的模块来引导组件。

***测试失败！***

## 实现组件

首先，组件应该显示每个联系人的项目列表。

我们在由 Angular CLI 生成的*contact-list . component . spec*中创建一个测试用例。在写它的时候，我们再次假设所有的依赖关系都已经存在。

```
it('should display an element for each contact', function () {
  component.contacts = [new Contact('DummyContact'), new Contact('DummyContact2')];
  fixture.detectChanges();

  const contactElements: NodeList = fixture.nativeElement.querySelectorAll('.contact');

  expect(contactElements.length).toBe(2);
}); 
```

Enter fullscreen mode Exit fullscreen mode

代码无法编译。在组件的控制器*Contact-list . components . ts*中，我们需要一个数据结构 *Contact* 和一个属性*contacts*来提供 contacts_。_

我们创建了类 Klasse*/model/contact . ts*

```
export class Contact {
  constructor(public name: string) {
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并通过属性`contacts: Contact[]`扩展控制器。

***测试失败！*** 这一次我们用`ng test`启动 karma 测试服务器和运行程序，并观察到书面测试失败，因为我们期望两个 contact 元素当前没有显示。

我们保持因果报应的运行，因此可以获得关于我们的变化及其对行为的影响的即时反馈。

为了给每个联系人显示一个条目，我们扩展了视图*contact-list.component.html*。

```
<div>
  <ul>
    <li *ngFor="let contact of contacts" class="contact"></li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

***测试成功！*** 测试成功！

到目前为止，我们希望显示联系人的姓名。

```
it('a contact element should display the contact´s name', function () {
  const contact = new Contact('SomeName');
  component.contacts = [contact];

  fixture.detectChanges();

  const nameElement: HTMLElement = fixture.nativeElement.querySelector('.contact .name');
  expect(nameElement).not.toBeNull();
  expect(nameElement.textContent).toEqual(contact.name);
}); 
```

Enter fullscreen mode Exit fullscreen mode

***测试失败！*** 果然测试失败。

我们制作列表来显示名字。

```
<div>
  <ul>
    <li *ngFor="let contact of contacts" class="contact">
      <span class="name">{{contact.name}}</span>
    </li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

***测试成功！*** 我们的组件现在显示联系人的姓名。

我们结束了吗？我们来看看 e2e 测试。

***测试失败！*** 他们还是失败了。我们预计“Max”和“Moritz”最初会上市。

我们希望组件最初包含联系人“Max”和“Moritz”。

```
it('should initially display the contacts "Max" and "Moritz"', function () {
  const nameElements: NodeList = fixture.nativeElement.querySelectorAll('.contact .name');
  const names: string[]  = Array.from(nameElements, nameElement => nameElement.textContent);
  expect(names).toContain("Max");
  expect(names).toContain("Moritz");
}); 
```

Enter fullscreen mode Exit fullscreen mode

***测试失败！*** 现在我们也有一个失败的组件测试。

我们用该触点初始化控制器的触点属性。

***测试成功！*** 所有组件测试成功！

***测试成功！*** 现在 e2e-test 也是绿色的，故事的实现也完成了！

> 联系人管理
> 
> √应显示联系人列表，最初包含“Max”和“Moritz”

## 进一步发展

现在我们的骨骼行走，我们可以继续进一步发展。

接下来可能采取的步骤包括:

*   重构:提取特定于联系人列表的内容并将其委托给自己的页面对象，以保持联系人页面对象的整洁，并在其他测试中重用它。
*   故事:创建新的联系人
    *   通过在文本框中输入联系人姓名，然后按下 enter 按钮，创建新的联系人
    *   创建新联系人时，应该清除文本字段
*   优化:管理冗余存储中的数据

## 结论

像 Jasmine、Karma 或量角器这样的框架非常好地支持 web 应用程序的测试驱动开发。

在 Angular 的情况下，Angular CLI 创建的项目已经预先配置了 Jasmin、Karma 和量角器，开箱即用，可以通过测试来开发。

因此，TDD 对于 web 应用程序来说也是一种非常好的方法，可以在非常高的质量水平上创建经过测试和良好设计的源代码。

与结对编程和干净编码等其他方法相结合，可以有效地开发和维护复杂的应用程序。

另请参见[在测试驱动的 angular 应用中开发 redux 商店时的测试策略](http://maimart.de/test-strategies-when-developing-redux-stores-in-angular-apps-driven-by-tests)和[将 angular 项目推送到 aws 云](http://maimart.de/pushing-an-angular-project-to-the-aws-cloud)

帖子[教程:开发测试驱动的 angular app](http://maimart.de/developing-an-angular-app-driven-by-tests)最早出现在 [maimArt](http://maimart.de) 上。