# 开始使用黄瓜 BDD 进行自动化测试

> 原文：<https://dev.to/katalon/getting-started-with-cucumber-bdd-for-automation-testing-p7n>

> **截止日期:2021 年 10 月 25 日下午 23:59**
> **2021 年质量状况报告**
> *如果能知道同行和专家如何在质量保证游戏中保持领先，那该多好啊这就是我们创建《2021 年质量状况报告》的原因，该报告旨在向所有级别的专业人士收集最佳质量保证实践。
> 我们很高兴为**前 100 名受访者提供价值 30 美元的亚马逊礼品卡**，以及调查结果的最终报告。[提高嗓门！](https://www.research.net/r/medium-devto)T12】*

## 介绍

近年来，越来越多的软件团队在他们的开发过程中实施敏捷软件方法，以适应这个快速变化的市场。这种趋势对测试团队管理测试用例以及测试脚本提出了挑战，它们必须根据不断变化的需求进行维护。从一开始就找到合适的测试方法是成功的敏捷软件项目的关键要素之一。

## 黄瓜是什么？

事实上，许多敏捷团队已经成功地使用 Cucumber tool 为他们的测试过程实施了行为驱动开发(BDD)方法。那么，黄瓜是什么？为什么它和 BDD 一样，是为敏捷项目建议的合适策略？

黄瓜是一个用于运行以 BDD 格式创建的自动化验收测试的工具。该工具最突出的特性之一是能够将纯文本功能描述(用称为 Gherkin 的语言编写)作为自动化测试来执行。让我们看看下面的例子:

```
Feature: Update password
  Scenario: Admin user can update the user password
Given I am in the HR system with an Admin account
When I update password of another user
Then I receive a message for updating password successfully
And user password is updated to the new password
```

This incredible feature of Behavior-Driven Development (BDD) approach with the advantages as below:

*   用一种无所不在的语言编写 BDD 测试，这是一种围绕领域模型构建的语言，被包括开发人员、测试人员、BAs 和客户在内的所有团队成员广泛使用。
*   连接软件团队的技术和非技术成员。
*   允许与开发人员的代码直接交互，但是 BDD 测试是用一种也可以被商业利益相关者理解的语言编写的。
*   最后但同样重要的是，验收测试可以自动执行，而由业务涉众手动执行。

## **Cu** **女友有助于增进交流**

Cucumber helps improve communication between technical and non-technical members in the same project. Let’s have a look at the below requirement and its automation tests:

```
As an Admin User,
I would like to change the password of other user's accounts.
Feature: Update password
  Scenario: Admin user can update the user password
    Given I am in the HR system with an Admin account
    When I update password of another user
    Then I receive a message for updating password successfully
    And user's password is updated to the new password
```

With TestNG, the above test scenario can be implemented as below:

```
@test
public void testAdminUserCanUpdateUserAccountPassword() {
  // create users 
  User userAdmin = new User(UserRole.ADMIN, username, password);
  User user = new User(UserRole.VIEWER, user_username, user_password);

  // use Admin user to update another user password
   String message = userAdmin.updatePassword(user, user_new_password);

// verify password changed
   Assert.assertEquals(message, "Password changed successfully");
   Assert.assertEquals(user.getPassword(), user_new_password);
}
```

The same test case can be written using Cucumber:

```
Feature: Update password
  Scenario: Admin user can update the user password
    Given I am in the HR system with an Admin account
    When I update password of another user
    Then I receive a message for updating password successfully
    And user's password is updated to the new password
```

Both automation test scripts above perform well to complete the test automatically. But do all testers of your team make out these tests? Can other business analysts and other stakeholders use these tests again at the acceptance testing (AT) stage? The automation test with TestNG may be difficult for most manual testers and BAs to catch up with. Moreover, it is impossible to use this test again for AT. As a result, based on these flaws mentioned before, this can not be considered as a suitable method. In contrast, [Automation Testing with Cucumber BDD](https://www.katalon.com/resources-center/blog/cucumber-bdd-automation-testing/) is created in a business domain language or in natural language, which can be easily made out by all members of the software project team. Communication is crucial for any development team, especially in the Agile team. There are usually many continuous chats, discussions, or even arguments happening among developers and testers in order to figure out what the correct behavior of a feature is. By using Cucumber, the same feature specification is now used for developing by developers, for testing by testers. It is considered to be a powerful tool because it can help lower the risk for misunderstanding as well as the communication breakdown.

## **Cucumber 是一个自动化验收测试工具**

The acceptance test typically is carried out by BAs/customers to make sure that the development team has built exact features. Typical activity in this testing stage is verifying the system against the original requirements with specific, real data from production. Cucumber testing not only follows the requirements as its test scenarios but also helps BAs or Product Manager to easily adjust test data. Here is a demonstration with a little adjustment:

```
As an Admin User,
I would like to change the password of other user's accounts.

Feature: Update password
  Scenario: Admin user can update the user password
    Given I am in the HR system with an Admin account
    When I update password of another user
    Then I receive a message for updating password successfully
    And user's password is updated to the new password
```

The automation test written in Cucumber framework:

```
Scenario Outline: Verify Updating user password feature
  Given I am in the HR system with "<account_type>" account
  And there is another user with "<old_password>" password
  When I update password of the user to "<new_password>"
  Then I got the message "<message>"
  And the user password should be "<final_password>"

Examples:
|account_type  |old_password  |new_password |message                |final_password |
|Admin         |$Test123      |@Test123     |Password changed..     |@Test123       |
|Viewer        |$Test123      |@Test123     |Invalid right access.. |$Test123       |
```

## 所有测试人员都可以参与黄瓜的自动化测试

In addition to improving communication among members of the same testing team, Cucumber also helps leverage tester’s skills efficiently. Expertise gap always exists in every organization. In other words, there are some testers who have high technical expertise in programming utilizing automated testing, while others performing manual testing with limited programming skills in the same team. Thanks to Cucumber, all testers, no matter what their skill levels are, can participate in the process of performing automation tests. Let’s take a look at the above example:

*   任何了解业务逻辑和工作流程的测试人员都可以编写特性文件，添加更多的场景，并测试数据集。
*   任何具有编程基础知识并知道如何创建对象、访问属性、调用方法的测试人员都可以生成步骤定义。
*   任何具有较高编程技能水平的测试人员都可以参与制作框架、定义数据源连接等过程。

There are still a few potential issues when implementing Cucumber:

*   Cucumber 使用业务领域知识帮助运行纯文本文件中指定的测试场景。因此，语言的使用和创建测试的人的理解可能会直接影响测试场景，导致误解的风险。测试场景应该清晰地呈现，并且它们的实现应该在每一步都准确地执行。例如，当您想要验证 Google 上的搜索特性时，测试应该是:

```
Scenario: performing a search on google
Given I am on "www.google.com" site
When I search for "Cucumber and BDD"
Then ...
```

These steps may be incorporated to have the following test:

```
Scenario: performing a search on google
When I search for "Cucumber and BDD"
Then ...
```

The stages of the Cucumber tool are performed in an ordinary language. They can be used again in various test scenarios. This helps reduce the effort to create tests. However, maintaining the test to be both readable and reusable is really a big challenge. If the test is written at a very high level for any stakeholders to make out; few steps (bold) can be reused:Both the above scripts are correct; however, the second one is not apparent because it does too much more than expected: opening Google’s website and searching with the specified text. Imagine if you want to extend the test to search more texts, you may repeat the above step, and the Google site is consequently opened twice. If you do not strictly follow the requirement, the Cucumber testing tool will cause misunderstanding sooner or later and be so difficult to maintain when being extended.

```
Feature: Update password
  Scenario: Admin user can update the user password
    Given I am in the HR system with an Admin account
    When I update password of another user
    Then I receive a message for updating password successfully
    And user's password is updated to the new password

  Scenario: Viewer user cannot update the user password
    Given I am in the HR system with a Viewer account
     When I update password of another user
     Then I receive a message for not able to update the user password
     And user's password remains the same
```

In contrast, if the test is generic and can be reused, i.e., verifying updating user’s Last Name, non-technical stakeholders will have difficulty in catching up with and performing Acceptance Tests:

```
Scenario: Admin user can update user password:
  Given I am in the "$System.HR_Page" with "admin@test.com" username
and "$Test123" password
  And there is another user in "$System.HR_Page" with "user@test.com" 
username and "$Test123" password
  When I update "$UserTemplate.Password" of "user@test.com" user to"@Test123"
  And I save the response message as "response_message"
  Then "$response_message" should be "Password changed successfully"
  And the  "user@test.com" user's "$UserTemplate.Password" should be"@Test123"
```

During the testing process, you have to adjust test scenarios regularly until they completely reach an acceptable balance where all members can understand and reuse.

```
Scenario: Verify Updating user password feature
  Given I am in the HR system with "Admin" account
  And there is another user with "$Test123" password
  When I update password of the user to "@Test123"
  Then I got the message "Password changed successfully."
  And the user password should be "@Test123"
```

Or with some more test data:

```
Scenario Outline: Verify Updating user password feature
  Given I am in the HR system with "<account_type>" account
  And there is another user with "<old_password>" password
  When I update password of the user to "<new_password>"
  Then I got the message "<message>"
  And the user password should be "<final_password>"

Examples:
|account_type |old_password |new_password |message           |final_password |
|Admin        |$Test123     |@Test123     |Password changed..     |@Test123  |
|Viewer       |$Test123     |@Test123     |Invalid right access.. |$Test123  |

```

## **想开始使用黄瓜的测试团队的重要注意事项**

*   将自动化测试视为与真实项目一样重要。代码应该遵循编码实践、惯例等。
*   应该考虑一个合适的编辑器工具。这个编辑器应该有助于调试和编辑标准文本格式的特征文件。Aptana(免费编辑器)、RubyMine(商业编辑器)和 [Katalon Studio](https://www.katalon.com) 是完全支持基于 BDD 的 Cucumber 的合适选择。
*   最后但同样重要的是，使特征文件成为一个真正的“通信”层，在这里你可以存储接收到的测试数据并格式化测试数据。不包含域业务逻辑。

总之，通过在一个健壮的测试框架之上提供真正的通信层，Cucumber 不仅可以从后端到前端对广泛的测试需求执行自动化测试，还可以在测试团队成员之间建立深层联系。这个特性在其他测试框架中几乎找不到。凭借在构建和应用自动化测试领域多年的专业知识，我建议将 Cucumber for Web UI 和 Web 服务测试应用于敏捷软件项目。[使用基于 Cucumber 的 BDD 和 Katalon Studio 作为编辑器](https://www.katalon.com/faqs/#katalon-studio-support-bdd)是一个正确的选择，它可以极大地帮助减少执行回归测试的工作量。

*作者:仲布依-QAA KMS-科技*