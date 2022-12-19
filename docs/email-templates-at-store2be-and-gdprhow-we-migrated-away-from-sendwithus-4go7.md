# store2be 和 GDPR 的电子邮件模板——我们如何从 Sendwithus 迁移

> 原文：<https://dev.to/peterfication/email-templates-at-store2be-and-gdprhow-we-migrated-away-from-sendwithus-4go7>

当 3 年前开始使用 store2be 时，我们一直在寻找一种处理电子邮件模板和发送的好方法。我遇到了 [Sendwithus](https://www.sendwithus.com) ，一个电子邮件模板服务，连接到许多不同的电子邮件提供商，如 SendGrid、Mailjet 等。

我们决定使用 Sendwithus，因为它将电子邮件模板从我们的主应用程序中分离出来，并允许非开发人员处理电子邮件模板的更改。此外，自动集成不同的电子邮件发送提供商非常有帮助。有一次，我们不得不更换电子邮件提供商，使用 Sendwithus 只需几分钟。

既然 GDPR 即将生效，我们必须评估我们使用的所有服务，并检查它们是否合规。二月份，Sendwithus 向其用户告知了他们处理 GDPR 合规性的方式:

> 不幸的是，我们将不会使 Sendwithus GDPR 兼容。

这对我们来说是个打击。尽管他们现在提供新的合规服务，但当我们听到 Sendwithus 说他们不会尝试 GDPR 合规时，我们立即开始寻找解决方案。因此，我们试图找到另一个符合我们要求的服务，但没有成功。

* * *

在 store2be，我们非常关注代码质量和相关工具(测试、林挺等)。).这一直是 Sendwithus 的问题。它有点工作，但我们从来不确定我们是否会破坏一些东西，并且审查只发生在视觉上，而不是通过查看实际的代码。此外，没有关于更改的好的 Git 历史记录。最后，有很多方法可以绕过 Sendwithus 的模板可能性的限制，例如关于代码片段。

最后，我们决定将电子邮件模板再次交给开发人员。主要原因可能是 Mailjet 开源了它的电子邮件模板标记语言 [MJML](https://mjml.io) ，这使得编写 HTML 电子邮件模板变得非常容易。在前端我们主要是用 React in Typescript 和 Jest 进行开发测试。就代码质量、可测试性和易用性而言，这似乎非常适合这个项目。

当然，这种方法失去了一个非常重要的属性:所有电子邮件模板的更改都必须由开发人员重新完成。

* * *

开源项目 [Maily](https://github.com/inventid/maily) 为如何开始使用这项服务提供了很多灵感([这里是 Maily](https://medium.com/@Rogier.Slag/creating-emails-with-the-maily-api-a-how-to-part-1-7f63306a7ad4) 的创建者关于它的一个中型帖子)。不幸的是，它不再被维护，我的问题和减贫战略没有得到解决。但是在它的核心，Maily 只是一个创建 express 服务器的文件。因此，我们将这个文件复制到我们的存储库中，并根据我们的需要进行调整(将它移到 Typescript，满足 linter，更新 MJML，添加更多功能)。

这就是我们现在正在做的:

*   **Typescript:** 我们所有的电子邮件模板代码都在 Typescript 中。因此，许多 bug 在早期就被发现了。
*   我们使用 TSLint 来遵循我们喜欢的编码标准。
*   更漂亮:我们用更漂亮来格式化我们的代码。不讨论每一种编码风格。
*   **测试:**所有组件(片段和电子邮件模板)都经过单元和快照测试。这意味着每个开发人员都对更改电子邮件模板充满信心。此外，我们使用 [lorikeet](https://github.com/cetra3/lorikeet) 进行集成测试。这增加了一层我们在开始时没有想到的额外的安全性。
*   **本地化:**我们使用一种非常简单的方法，每个电子邮件模板都有一个 JSON 文件，其中包含我们想要支持的每种语言的关键字。所以实际的 React 组件不包含任何文字，而是使用 translate 函数来读取这个 JSON 文件。TXT 和 HTML 模板使用相同的翻译，这降低了不一致的可能性。
*   **Preview:** 对于开发，您向本地 express 服务器发出 GET 请求(此时没有热重装)以查看电子邮件的预览。在线上，产品团队可以对临时或生产服务器做同样的事情。此外，我们有电子邮件模板的 Swagger 定义，可以转换成 Postman 集合，这使得产品团队的生活更加轻松。
*   **审查:**审查 store2be 的所有代码。这也适用于新的电子邮件模板服务。

总而言之，我们对自己开发电子邮件模板服务的决定非常满意。电子邮件模板终于变得有趣了。

下面是一个电子邮件模板现在的样子:

```
import { generateFetchLocale } from 'lib/utils'
import * as React from 'react'

import Button from 'templates/html/snippets/Button'
import Closing from 'templates/html/snippets/Closing'
import Footer from 'templates/html/snippets/Footer'
import FullWidthBorder from 'templates/html/snippets/FullWidthBorder'
import Greeting from 'templates/html/snippets/Greeting'
import Header from 'templates/html/snippets/Header'
import Layout from 'templates/html/snippets/Layout'
import Text from 'templates/html/snippets/Text'
import Title from 'templates/html/snippets/Title'
import locales = require('templates/locales/Welcome.json')

const Welcome: React.SFC<WelcomeProps> = props => {
  const link = props.link || 'https://www.store2be.com'
  const user = props.user || { title: '', lastname: '' }
  const { locale } = props
  const fetchLocale = generateFetchLocale(locale, locales)
  return (
    <Layout env={props.env}>
      <Header />
      {fetchLocale('title')} 
      <Greeting locale={locale} lastname={user.lastname} title={user.title} /> 
      <Text>{fetchLocale('welcome_please_confirm')}</Text> 
      <Button link={link}>{fetchLocale('button')}</Button> 
      <Text>
        {fetchLocale('button_not_working') + '  '}
        <a href={link}>{link}</a>
      </Text> 
      <Closing locale={locale} /> 
      <FullWidthBorder />

      <Footer locale={locale} />
    </Layout>
  )
}

export default Welcome 
```

Enter fullscreen mode Exit fullscreen mode

* * *

[![Example email preview.](../Images/ba5a7ae70d97ae06c8142e34b81e8ef7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6VPjZL6l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tech.store2be.com/images/email-template.jpg)

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，致力于为短期零售空间建立一个 SaaS 式的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的 [Twitter](https://twitter.com/store2be_tech) 。*