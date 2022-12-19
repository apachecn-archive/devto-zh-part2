# 来自 Katalon Studio 的全新智能 XPath 生成器

> 原文：<https://dev.to/testingnews1/the-brand-new-smart-xpath-generator-from-katalon-studio-2ebo>

[![The Brand-new Smart XPath Generator from Katalon Studio](../Images/041cbc3e83ef12e1df34f635e301188d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VtfMuPYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/06095757/Smart-XPath-generator1.png)

## **简介**

在 Web UI 自动化测试中，测试人员主要关心的是测试脚本的高维护成本，这通常是由不稳定和损坏的定位器引起的。定位器是 Selenium 在测试执行期间用来标识元素的一种机制。流行的定位器机制是 XPath。不幸的是，当被测应用程序(AUT)改变时，定位器很容易被破坏或者不能识别目标元素。一项现有的研究表明，在 Web UI 测试脚本中，定位器占了 70%以上的漏洞。

因此，创建和使用正确且有弹性的定位器对于 Web UI 测试自动化的成功至关重要。

在本文中，我们描述了一种全新的智能方法，该方法在 Katalon Studio 中引入，用于在测试记录阶段自动生成健壮的定位器。这种先进的方法产生了新的定位器，与 Selenium 和其他测试自动化工具相比，这些定位器更易于维护，更不易受到 AUT 中的变化的影响。

## **普通定位器不适用的情况**

让我们看看 ted.com，一个使用 EmberJS 框架构建的网站。下面的截图显示了网站的卡片，每张卡片包含不同的项目(文本、图像、链接等。).

[![common locators are inadequate](../Images/4537d63e974d50743dbb60772542c9c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XLTqdCo2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05140247/common-locators-are-inadequate.png)

假设您想要记录一个点击视频缩略图的简单操作，例如，带有“人工智能如何拯救人类”的视频，您将获得 Selenium 生成的以下 XPaths:

| **Selenium 的 XPath** |
| //div[[@ id](https://dev.to/id)= ' react-container 1 ']/div/div/div/div/div[2]/div/div/div/a/div/div[2] |
| //div[2]/div/div[2]/div/div/div/a/div/div[2] |

虽然这些 XPaths 可以定位元素，但是它们太长太复杂了。但主要问题是它们很容易被破坏，因为它们只有在内容不变的情况下才能工作。TED 是一个快速发展的网站，每天都有新的内容产生。所以，今天第一个视频可能明天就是第三个视频也就不足为奇了。如果你看看这些 XPaths，或者只看最后 3 个字符，你会注意到它们完全依赖于视频的顺序不会改变的假设。如上所述，这种假设与现实相矛盾。简而言之，这些定位器很脆弱，难以读取和维护。

再举个例子，Linkedin 上的微软个人资料。

[![Microsoft-profile-on-Linkedin](../Images/201674f7e3b40a8f7331d4467468ddad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_om66Mft--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05140753/Microsoft-profile-on-Linkedin.png)

假设您想要验证 Microsoft 的配置文件信息，并使用 Selenium 通过单击每个字段来获取 XPaths，您将得到以下结果:

| **字段** | **Selenium 的 XPath** |
| 网站(全球资讯网的主机站) | id=ember1801 |
| 总部 | //div[[@ id](https://dev.to/id)= ' org-about-company-module ']/div/div[2]/div/p |
| 创建年份 | //div[[@ id](https://dev.to/id)= ' org-about-company-module ']/div/div[2]/div/p[2] |
| 公司类型 | //div[[@ id](https://dev.to/id)= ' org-about-company-module ']/div/div[2]/div/p[3] |
| 公司规模 | //div[[@ id](https://dev.to/id)= ' org-about-company-module ']/div/div[2]/div/p[4] |
| 特别 | //div[[@ id](https://dev.to/id)= ' org-about-company-module ']/div/div[2]/div/p[5] |

让我们逐行检查上面的表格。网站字段按其 ID 定位，似乎是一个合理的选择。您现在可以说，至少您的测试用例将成功地定位这个字段。但是，你以后会发现不会，因为每次访问这个页面 ID 都不一样。换句话说，这个 ID 是动态的。所以在这种情况下使用 ID 作为定位器并不是一个好的选择。

总部字段由一个相对简洁的 XPath 定位。不幸的是，这个定位器识别了五个 Web 元素，而不是预期的唯一元素。(您可以通过打开 Chrome 并按 Ctrl + Shift + I 打开检查工具并在文档中搜索 XPath 来检查这一点——如下所示)。

[![Inspection-Tool-and-search-the-XPath-in-the-document](../Images/f58df2e3f3c88c837270319b065a68f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SOSiiILO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05140944/Inspection-Tool-and-search-the-XPath-in-the-document-300x220.png)

这个结果并不令人惊讶，因为在公司信息部分中确实有五个 Web 元素带有标签<***【p】>***。有些回放引擎会自动选择这五个元素中的第一个，但有些不会。

剩下的 XPaths 遇到了我们已经在 TED 网站上讨论过的同样的问题。

## **邻居 XPath:一种适合上述情况的 XPath 形式**

如果一个元素不能使用它的直接属性被一致地定位，那么使用它的更健壮的邻居来识别它。这是 Katalon Studio 中引入的新定位器方法背后的一个简单而强大的想法。这种方法在视觉上很直观，因为它反映了用户经常在用户界面上识别可视元素的方式。

由方法生成的 XPath(也称为邻居 XPath)遵循以下形式:

| one | Two | three | four | five |
| xpath=(。//*[normalize-space(text())和 normalize-space(。)=' **neighbor_text** ']) | [n]/ | 前面:: | 标签名称 | [m] |
| 以下内容: |

这种形式假设目标元素的一个邻居是健壮的，并且由它的文本来标识。该表格中的每一栏解释如下:

| **列号** | **离散描述** |
| one | 使用相邻元素的文本检索元素 |
| Two | 使用邻居的索引在步骤 1 中检索的邻居中检索邻居。 |
| three | 从邻居，向前或向后 |
| four | 检索与目标元素具有相同标记名的所有元素，然后 |
| five | 检索目标元素 |

让我们回到 TED 的例子:

[![the example with TED](../Images/1d20c6a4538e94a85eecdba3f03c0214.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lsD_i9GS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05141315/the-example-with-TED.png)

这里有几个采用上述形式的 XPaths，以识别“人工智能如何拯救人类”之前的缩略图。

| **邻居 XPath** |
| xpath=(。//*[normalize-space(text())和 normalize-space(。)= '人工智能如何拯救我们人类'])[2]/preceding::div[3] |
| xpath=(。//*[normalize-space(text())和 normalize-space(。)= '最新谈话'])[1]/following::div[11] |

*"xpath=(。//*[normalize-space(text())和 normalize-space(。)"*零件是固定的，所以我们在维护时可以忽略。相反，我们关注的是其余部分，即 UI 中显示的标签，因此它是可读的。

第一个 XPath 是最直观的，因为它符合用户处理缩略图以及与缩略图交互的方式——代表缩略图的标签。这个定位器很容易维护，因为当 UI 上的标签改变时，您只需更新脚本中相应的文本。此外，视频的整体模板往往比内容更稳定(例如，YouTube 多年来一直没有改变它的模板)。因此，尽管物品之间的动态排序*,复合物品或卡片中的目标元素和其相邻元素之间的关系被保留。*

对于 Microsoft 配置文件示例:

[![Microsoft profile example](../Images/45fe7ba39bacb775d9c1ba6d81151847.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_elL4dI0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05141613/Microsoft-profile-example-2.png)

以下是相应的邻居 XPaths:

| **字段** | **邻居 XPath** |
| 网站(全球资讯网的主机站) | xpath=(。//*[normalize-space(text())和 normalize-space(。)= '网站'])[1]/以下::a[1] |
| 总部 | xpath=(。//*[normalize-space(text())和 normalize-space(。)= '总部'])[1]/以下::p[1] |
| 创建年份 | xpath=(。//*[normalize-space(text())和 normalize-space(。)= '成立年份'])[1]/following::p[1] |
| 公司类型 | xpath=(。//*[normalize-space(text())和 normalize-space(。)= '公司类型'])[1]/以下::p[1] |
| 公司规模 | xpath=(。//*[normalize-space(text())和 normalize-space(。)= '公司规模'])[1]/following::p[1] |
| 特别 | xpath=(。//*[normalize-space(text())和 normalize-space(。)= '特长'])[1]/以下::p[1] |

同样，忽略上面 XPath 的固定部分，我们可以看到微软的 profile 的字段嵌入在 XPath 中。在测试执行过程中，如果一个字段丢失，那么执行将被停止，以准确地指出错误所在。但是，在 Selenium 中，由于删除了一个项目，订单发生了变化，因此会显示错误的失败项目。

## **在 Katalon Studio 中使用智能 XPath 生成器**

当然，您不必太担心如何像上面那样创建邻居 XPaths。Katalon Studio 会自动完成这项工作。邻居 XPaths 是在记录期间生成的。Katalon Studio 试图选择合适的 XPathss，而您仍然可以手动选择最相关的 XPath，如下所示。

[![Using Neighbor XPath generator in Katalon Studio](../Images/b0d2e0974da25da5bfc8ccc302be3ef9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z6EMclMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05141902/Using-Neighbor-XPaths-in-Katalon-Studio.png)

通过自定义定位器策略设置，您可以为所有测试元素的智能 XPaths 选择默认策略。

[![Automatic XPaths](../Images/f6a2a00b6be4b161f168c053a275bc24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Csk2sebT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1h3p5fzmizjvp.cloudfront.net/wp-content/uploads/2018/09/05150515/Automatic-XPaths.png)

## **结论**

我们展示了几个例子来证明 Selenium 的 XPaths 是脆弱的并且难以维护。常见原因是硒元素依赖于元素的索引和排序。

本文描述了 Katalon Studio 在自动化测试脚本中自动生成定位器或邻居 XPaths 的高级智能方法。与 Selenium 生成的 XPathss 相比，XPath 对 AUT 中的变化更有弹性，也更容易维护。这种高级方法在 Katalon Studio 的 5.7 版本中可用。[了解有关 5.7 版本的更多信息](https://www.katalon.com/resources-center/blog/smart-xpath-bdd-testing-katalon-v5-7/)

阅读更多信息:

*   [在 Katalon Studio 中使用 XPath 检测元素](https://www.katalon.com/resources-center/tutorials/detect-elements-xpath/)
*   [在 Katalon Studio 中使用 XPath 的常见技巧](https://www.katalon.com/resources-center/tutorials/xpath-katalon-studio-tips/)

原始来源:[探索全新的 Katalon Studio 智能 XPath 生成器](https://www.katalon.com/resources-center/blog/smart-xpath-generator/)