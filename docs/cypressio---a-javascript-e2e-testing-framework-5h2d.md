# Cypress.io -一个 JavaScript E2E 测试框架

> 原文：<https://dev.to/committedsw/cypressio---a-javascript-e2e-testing-framework-5h2d>

[![cypress logo full](img/3732bbee604424de70d9be4b68d4849d.png)T2】](///static/cypress-logo-full-48359ea995d0302b2a703171a78c801f-8f9c4.png)

端到端(E2E)测试是任何开发周期的关键部分，到目前为止，E2E UI 测试主要是使用 [Selenium WebDriver](https://www.seleniumhq.org/projects/webdriver/) 完成的。不过现在，一个新的测试框架 [Cypress](https://www.cypress.io/) 已经可以使用了。它不同于 Selenium，提供了几个非常有用的改变游戏规则的特性，例如:

*   异步 DOM 元素查找
*   服务器存根
*   “时间旅行”调试

### 设置

设置再简单不过了——Cypress 只是一个 npm 包，所以可以作为一个包安装，并与`cypress open`交互运行，或者以无头模式与`cypress run`运行

### 异步设计

任何有编写 Selenium 测试经验的人都可能知道同步的痛苦:在某些元素加载到 DOM 之前处理测试运行的问题。Cypress 通过使用 JavaScript promises 解决了这个问题。不再需要像在 Selenium 中那样手工编写隐式或显式等待，因为隐式等待被有效地内置了。

```
describe('a sample test', () => {
  it('should do stuff', () => {

    cy.visit(appUrl) // Go to appUrl

    cy.get('.some-button').click() // Wait for .some-button to be present, then click on it

    cy.get('.some-other-button', ($btn) => {
      // do stuff with the element $btn
    })

    cy.get('.yet-another-button', ($btn) => {
      // do stuff with the element $btn
    }, {timeout: 60000}) // If Cypress doesn't find the element within 60000 ms, then error
  })
}) 
```

### 服务器存根

Cypress 有内置的功能来存根/模拟任何链接到 UI 的后端服务器。这有两个主要优点:

*   我们可以通过对 API 的每一个请求进行存根响应来消除对后端的依赖。
*   我们可以模拟一些响应——例如，登录尝试，或者我们不希望测试依赖的第三方 API。

```
describe('another sample test', () => {

  beforeEach(() => {
    cy.server() // Enable response stubbing
    cy.route({
      method: 'GET', // when the app sends a GET request
      url: 'http://www.some-api.com/something', // to this URL
      response: {message: 'Some mocked message'} // then return this response
    })

    cy.visit(appUrl)
  })

  // Tests...
}) 
```

### 时间旅行调试

即使有一个简单的设置和内置支持来解决同步问题，Cypress 还有另一个开发人员的宝贝。交互式“时间旅行”调试。在一个简单的 UI 中，用户可以看到正在运行的测试中的所有步骤，以及在每个步骤之前、之中和之后发生了什么。

[![](img/7c5e30af38a32ab0d41b904da06aab55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q6dk7M-g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://committed.software/cypress-debugging-c6efb7c87295a6da1d521b32dadf9b7f.gif)

### 持续整合

当涉及到 E2E 测试时，持续集成经常是棘手的。就运行 Cypress 测试而言，这很简单——这只是一个 npm 命令。潜在的困难在于将 UI(也可能是 API)作为管道的一部分来运行。我们通过使用 [Docker Compose](https://docs.docker.com/compose/) 将 UI 和后端的实例作为 [Docker](https://www.docker.com/) 容器来实现这一点，尽管这显然取决于项目的基础设施和部署方法。

### 结论

Cypress 作为 UI 测试解决方案有着惊人的潜力。它不仅易于启动和运行，还解决了 Selenium 最头疼的问题之一——同步。通过使用 promises 做到这一点，对于大多数 JavaScript 开发人员来说，习惯这一点应该很容易。时间旅行调试允许容易地洞察测试失败的位置和原因，并且存根部分或全部后端的能力允许只测试 UI、整个系统或两者的混合。