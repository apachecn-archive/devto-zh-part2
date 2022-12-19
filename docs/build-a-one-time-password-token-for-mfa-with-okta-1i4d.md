# 用 Okta 为 MFA 构建一次性密码令牌

> 原文：<https://dev.to/oktadev/build-a-one-time-password-token-for-mfa-with-okta-1i4d>

Okta 有一个很棒的多因素认证(MFA)服务，只要有一个[免费开发者账户](https://developer.okta.com/signup/)，你就可以马上使用它。它通过在身份验证后要求第二个因素来提供额外的安全性，并支持各种因素类型，包括 SMS、Google Authenticator 等软令牌、Yubikey 等硬令牌以及带有推送通知的 Okta Verify 软令牌。

Google Authenticator 和 Okta Verify 是一种称为基于时间的一次性密码(TOTP)令牌的因素。他们使用一种基于共享秘密和高度精确的系统时钟的算法。Okta 通过在 Okta Verify 移动应用程序中支持推送通知，在不牺牲安全性的情况下增加了额外的便利性。

Okta Verify 在注册 MFA 时使用一个二维码读入共享密钥。

在这篇文章中，我以一种不太方便但有趣的方式使用共享秘密，同时仍然保持相同的安全级别。ArduBoy 项目结合了一个 Arduino 微处理器，一个单色有机发光二极管屏幕和一组按钮，看起来很像经典的任天堂游戏机。所有这些都适合信用卡大小的外形。这个开源编程平台是使用 TOTP 标准为 MFA 创建基于硬件和软件的混合令牌的理想工具。

嗯，也许不完美。但是，好玩！

 <video src="https://developer.okta.com/assets/blog/ardu-token-mfa/oktaardutoken-92bb231be14e4a050e2bd802899651ef5d030abb615b926a3edea801c22876cd.mp4" controls="">最大的挑战是，当你关掉一个 ArduBoy 时，它真的是*关了。当 ArduBoy 关闭时，没有实时时钟继续运行。在我们的电脑或移动设备上，我们认为这是理所当然的，这些设备要么有保持时钟运行的硬件，要么有在开机时通过网络自动设置时间的能力，或者两者都有。阿杜博伊就不是这样了！为了真正将它作为你的随身 TOTP 设备，你需要让它一直开着，在它没电之前给它充电。*

应用程序采取的缓解措施是使用板载 EEPROM(电可擦除可编程只读存储器)来(a)保存密码和(b)保存上次设置的日期和时间。下次打开 ArduBoy 时，它会检查是否设置了密码。如果是，它将直接设置日期和时间。在日期和时间设置屏幕上，它从上次设置的日期和时间开始，以便于更新。

[![Ardu Token](../Images/7e48a3297c2b4c03b6f4db011515e01b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kEBOX5FS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/ardutoken-fade269e61f766f5d7b006b97a369520efbd963164d04da85aed70d8eeb8c01d.png)

这是一个有趣的方式来了解 TOTP，并看到它对一个真正的 Okta 组织的工作。

在“现实生活”中，你会想要使用 Okta Verify 移动应用程序(在 [iOS](https://itunes.apple.com/us/app/okta-verify/id490179405?mt=8) 和 [Android](https://play.google.com/store/apps/details?id=com.okta.android.auth&hl=en_US) 上可用)。涉及的体力劳动要少得多。

## 使用 Okta 验证令牌启动并运行

源代码(包括预构建的二进制文件)可以在 [GitHub 库](https://github.com/oktadeveloper/okta-ardu-token-example)中找到。

如果你有一个 ArduBoy，想要看到应用运行，可以直接安装最新的二进制`.hex`文件。

你也可以将`.hex`文件直接放到 [ProjectABE](https://felipemanga.github.io/ProjectABE/) ArduBoy 模拟器网站上，不用亲自动手就能看到它的运行。

**注意:** ProjectABE 不能将数据保存到 [EEPROM](https://www.arduino.cc/en/Reference/EEPROM) 中。因此，如果您在那里重新启动应用程序，您将需要重新设置共享秘密以及日期和时间。

将 OktaArduToken 安装到真正的 ArduBoy 上最简单的方法是使用 [Arduino IDE](https://www.arduino.cc/en/Main/Software) 。这允许您编辑和上传源代码，并提供命令行工具`avrdude`来上传二进制文件。

下面是一个在 Mac 上使用`avrdude`的安装命令示例:

```
/Applications/Arduino.app/Contents/Java/hardware/tools/avr/bin/avrdude -v \
-C /Applications/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf \
-p atmega32u4 \
-c avr109 \
-P /dev/cu.usbmodem1411 \
-U flash:w:<path to OktaArduToken project>/OktaArduToken.hex 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*`-p`参数指定 ArduBoy 零件号，`-c`参数指定 ArduBoy 编程器类型。在你的 Mac 上，`-P`参数会有所不同。您可以使用以下命令查看可用串行端口列表:

```
ls -la /dev/cu.* 
```

Enter fullscreen mode Exit fullscreen mode

您正在寻找包含`usbmodem`的条目。

## 处理源代码和依赖项

如果你想在 Arduino IDE 中使用源代码，编译它，并上传到你的 ArduBoy，你需要安装一些库。对于每一项，请导航至:`Sketch -> Include Library -> Manage Libraries`。

你需要:

*   ardu boy 2-ardu boy 微型游戏系统的替代库
*   软件实时时钟
*   [TOTP-Arduino](https://github.com/lucadentella/TOTP-Arduino) -库生成基于时间的一次性密码
*   Base32 -一个将字符串编码到 Base32 中并从 Base32 中解码字符串的库

`Base32`库是唯一一个不能通过 Arduino IDE 中的库管理器安装的库。通过将 GitHub 库克隆到您本地的 Arduino IDE libraries 文件夹，安装起来非常容易。在 Mac 上看起来是这样的:

```
cd ~/Documents/Arduino/libraries/
git clone https://github.com/NetRat/Base32.git 
```

Enter fullscreen mode Exit fullscreen mode

如果您的所有库都已准备就绪，您可以在 Arduino IDE 中导航到:`File -> Open...`并选择`OktaArduToken.ino`文件。然后，您应该能够导航到:`Sketch -> Verify/Compile`来编译代码。如果出现任何错误，请确保安装了上述所有库。

## 在 OktaArduToken 界面导航

OktaArduToken 在 Arduino 或 ArduBoy 的 TOTP 示例中是独一无二的，因为它有一个设置共享秘密和设置日期和时间的接口。大多数示例要求您在上传之前将秘密硬编码到源代码中。

共有 6 个按钮，设置共享密码和日期时间的界面可能会让你想起 90 年代的翻盖手机。；)

### 设置共享秘密

当您第一次启动该应用程序时，您会看到共享密码设置屏幕:

[![Secret setting screen](../Images/f713008f9f16cffef0cf303e2e26a2ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0DOljZq3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/secret-ba725b8c450eb647a6c3b7816a17ecb28ca5ba5b8cb6f680bc6ceff9b993f104.png)

Okta Verify 使用 16 字节的 Base32 编码字符串作为共享密钥。最初，这显示为 16 个`M`秒。您可以使用向上和向下按钮在一组大写字母和数字 0 - 9 之间导航。您可以使用左右按钮在可用的 16 个字符内移动位置。

界面自动换行。也就是说，当你在第 16 个字符时点击右按钮，光标会回到第 1 个字符。当你在第一个字符上时，点击左键将光标移动到第 16 个字符。同样，当`9`显示时点击向上按钮，会将该字符变为`A`。当`A`出现时点击向下箭头，将会把那个角色变成`9`(它会变成`A` - `Z`，然后是`0` - `9`)。

设置共享密码后，按`A`按钮进入日期和时间设置屏幕。

*注:*见下文，了解如何为 MFA 配置 Okta 并获取共享密钥以进行编程。

### 设置日期和时间

保存共享密码后，您会看到日期和时间设置屏幕:

[![Time setting screen](../Images/fd9808c28967286858e8156d050d06ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V1ROkU3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/date-37d38a9226b3fe2f22667c05b3cab934e5995cdfaeb451d1c5060595edf761cc.png)

您可以使用向上和向下箭头来更改日期和时间的每个部分的数字。您可以使用左右按钮来改变日期和时间界面上的位置。该接口将自动跳过分隔符，并以类似于共享密钥接口的方式自动换行。

一旦设置好日期和时间，按下`A`按钮进入 TOTP 屏幕。

**注意:**精度很重要，所以建议你把时间拨快 10 秒，看一个带秒计数器的钟，在时间一致的时候按下`A`按钮。此外，无论您当前的时区是什么，您设置的日期和时间都应该是 GMT。此外，目前没有任何种类的错误检查。也就是说，如果您输入无效的日期和/或时间，将会得到意想不到的结果。

您可以按`B`按钮从这里返回共享密码屏幕。

### TOTP 显示

一旦设置了共享密钥和日期时间，您就会看到 TOTP 屏幕。在屏幕顶部，您会看到当前密码，字体很大。此密码每 30 秒更改一次。在密码下方，您可以看到每秒更新的完整日期和时间。

[![TOTP screen](../Images/7155d86634c892fb2b53c1fc6e395c67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fkmINADx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/totp-cfee5c2c2641f86f1e4c15b316df0ba25fe029dd16d5cc470e5d4bdf8e5f39d3.png)

按下`A`按钮返回设置日期和时间屏幕。按下`B`按钮返回到设置共享密码屏幕。

## 为多因素认证配置 Okta

在俄克拉荷马州，MFA 有两个补充部分:注册和执行。MFA 注册策略决定了要求用户注册 MFA 的条件以及用户必须注册的配置因素。可以将登录策略配置为在身份验证后要求第二个因素。这是该政策的外交部执行部分。

首先，在 https://developer.okta.com/signup/[注册一个免费的 Okta 开发者组织](https://developer.okta.com/signup/)

设置一个 Okta 组和一个新用户，以便更容易地测试 MFA 策略。

登录你的 Okta 组织的管理控制台。通过选择左上方的下拉菜单，从*开发者控制台*切换到*经典 UI* :

[![Classic UI](../Images/4ffe4f7e4318d782bcf2cdf0ab049f2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zoYVurlz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/classicui-b038239766d5cbce947c5ecbb08a52ca1ab973ccfe4d958ead327c93945907b8.png)

接下来，从管理控制台的顶部菜单中选择:**目录** > **组**。点击**添加组**，在*名称*字段输入`mfaers`。点击**添加组**。

从管理控制台的顶部菜单中选择:**目录** > **人物**。点击**添加人员**，填写如下字段:

| 领域 | 价值 |
| --- | --- |
| 西方人名的第一个字 | 简（女子名） |
| 姓 | 母鹿 |
| 用户名 | [jane.doe@mfaers.com](mailto:jane.doe@mfaers.com) |
| 主要电子邮件 | [jane.doe@mfaers.com](mailto:jane.doe@mfaers.com) |
| 组 | 马士基 |
| 密码 | 由管理员设置 |
| 用户必须在首次登录时更改密码 | (未选中) |

输入您选择的密码。点击*保存*。

[![Jane Doe](../Images/e2691739ec30e091af036bc63d614f08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5FpBWZIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/janedoe-85b15f78ca1f6504a43edcec57c4bf3a3ba3786de9f190c3980af7318b764fc0.png)

### 配置 MFA 注册策略

从管理控制台的顶部菜单中选择**安全** > **多因素**。

在因子类型选项卡上，选择 *Okta 验证*旁边的**激活**。

选择**因子登记**选项卡。点击**添加多因素策略**，输入*策略名称*的`mfaers policy`，选择`mfaers`为*分配到组*。从 *Okta 验证*旁边的下拉菜单中选择**必需的**。点击**创建策略**。

[![MFAers Policy](../Images/c3a2079569df38191bb6a72a2e4638e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A2IcVv8a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/mfaerspolicy-851a75057f85b6ced3d8d4165cf751df0919ea1a1d11d703acf945c68abef75a.png)

在*添加规则*对话框中，为*规则名称*输入`mfaers rule`，并在*注册多因子*旁边的下拉列表中选择*。点击**创建规则**。*

[![MFAers Rule](../Images/8be52660a5cf8e1b49235015471cbf28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oidYyfqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/mfaersrule-e1bf363fd76e4283e9017b6f26d36154b462e74840ac8ad181e1ece243e2774c.png)

这就是配置 MFA 注册所需的全部内容！

### 配置 MFA 实施策略

从管理控制台的顶部菜单中选择**安全** > **认证**。点击**签到** > **添加新的 Okta 签到策略**。

在*策略名称*中输入`mfaers policy`，在*中选择`mfaers`分配到组*。

点击**创建策略并添加规则**。

[![MFAers Sign-on Policy](../Images/a5cd62b961d58cd2cecf7f32543058ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vLWrmb_k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/mfaerssignonpolicy-68924aa72c45aa16de5e7bbd1fa49a5fe265c95a8b2a39a3f91f000009028260.png)

输入*规则名称*的`mfaers rule`，检查*提示因子*。选择**每次** > **创建规则**。

[![MFAers Sign-on Rule](../Images/6f005b2ad0a9c3a9c653043924939acd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jf6WKdAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/mfaerssignonrule-d5a07d06be58ac5e74d5762d636f19afe8fc01424f4824bd3f714511c01e3ae0.png)

这就是配置 MFA 强制所需的全部内容。

## 向 Okta 和 OktaArduToken 认证

在一个私人浏览窗口中，导航到您的 okta org 并以[jane.doe@mfaers.com](mailto:jane.doe@mfaers.com)的身份登录。

您应该会看到一个设置 Okta 验证的屏幕。点击**配置因子**。

[![Configure factor](../Images/2e4f812264a92c9f342265d100a75737.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h3W8CUjU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/configurefactor-8e04f4be9537ec01f29cb56853a9789b27d184ef70fc245ea5affc50df62daaa.png)

在下一个屏幕上，选择任何设备类型(没关系，因为我们将设置我们的 ArduBoy)。点击**下一个**。

在设置 Okta 验证屏幕上，点击**无法扫描？**

[![Can't scan?](../Images/4428644cf3517bb519e36910da4175c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QjzhdR_c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/cantscan-958b83f5503840b33954b166fa68d42cbab33cd819c9fd755abc7adcafb2ae1b.png)

然后，您将看到密钥字段。打开 ArduBoy(或使用 ProjectABE)并输入共享密钥值。

[![Shared secret](../Images/978e4c1c076cb613e427510496990ae4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Va_lc0oY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/ardu-token-mfa/sharedsecret-f42786960f630dc41c448a73e42f8eb34c405ec6359f62284bb8f81664b05f40.png)

在 Arduboy 上，按下`A`按钮，输入正确的日期和时间(GMT 时区)。按下`A`按钮。这将把你带到 TOTP 屏幕。

点击设置 Okta 验证对话框上的**下一个**。输入 ArduBoy 上显示的代码，点击**下一步**。

如果一切顺利，您将看到一个屏幕，要求您设置安全问题和答案，以完成您的帐户配置。

您现在已经使用 ArduBoy 作为硬件令牌完成了 Okta Verify 的注册！很酷的东西。

您可以注销并再次登录，系统会提示您再次输入从 ArduBoy 获得的代码。

**注意:**project Abe 中的时钟组件不是很准确，会很快落后或超前。您可以随时按下`A`按钮再次设置时间，以便显示正确的密码。

您也可以在移动设备上的实际 Okta Verify 应用程序中输入该值，并确认显示的密码与 ArduBoy 上的密码相同。

这是 OktaArduToken 与 Okta Verify 移动应用程序并排显示:

 <video src="https://developer.okta.com/assets/blog/ardu-token-mfa/oktaardusidebyside-cf0d8571e5c5fef9f84a0d64d6242466e4f1d8301aef24009f89da0ec40aadcf.mp4" controls="">## 一看 TOTP 的 Arduino 代码

OktaArduToken 的代码在一个单独的草图文件中:`OktaArduToken.ino`。我确信这不是最佳实践，并且会从一些 C++对象化中受益，但是它对一个快速的小爱好项目有效。

多亏了 TOTP 和 swRTC 库，`ShowTotpCode()`方法中的这一切都归结为三行代码:

```
TOTP totp = TOTP(hmacKey, 10);
long GMT = rtc.getTimestamp();
totpCode = totp.getCode(GMT); 
```

Enter fullscreen mode Exit fullscreen mode

它使用`hmacKey`(共享秘密的 Base32 解码值)和当前时间戳来计算当前的 totpCode。ArduBoy 屏幕上显示的就是这个 6 位数的代码。

另一个关键使能特性是能够将共享密钥和当前时间写入 EEPROM，并读出这些值。

```
void writeTotpInfo(TotpInfo totpInfo) {
  writeString(TOTP_SECRET_SAVE_ADDRESS, totpInfo.secret);
  writeInt(TOTP_SEC_SAVE_ADDRESS, totpInfo.sec);
  writeInt(TOTP_MIN_SAVE_ADDRESS, totpInfo.minu);
  writeInt(TOTP_HOUR_SAVE_ADDRESS, totpInfo.hour);
  writeInt(TOTP_DAY_SAVE_ADDRESS, totpInfo.day);
  writeInt(TOTP_MON_SAVE_ADDRESS, totpInfo.mon);
  writeInt(TOTP_YEAR_SAVE_ADDRESS, totpInfo.year);
  DEBUG_PRINTLN("Wrote totpInfo to eeprom.");
}

TotpInfo readTotpInfo() {
  TotpInfo ret = {};

  ret.secret = readString(TOTP_SECRET_SAVE_ADDRESS, 16);
  ret.sec = readInt(TOTP_SEC_SAVE_ADDRESS);
  ret.minu = readInt(TOTP_MIN_SAVE_ADDRESS);
  ret.hour = readInt(TOTP_HOUR_SAVE_ADDRESS);
  ret.day = readInt(TOTP_DAY_SAVE_ADDRESS);
  ret.mon = readInt(TOTP_MON_SAVE_ADDRESS);
  ret.year = readInt(TOTP_YEAR_SAVE_ADDRESS);

  return ret;
} 
```

Enter fullscreen mode Exit fullscreen mode

有许多支持方法来处理共享秘密、日期和时间的输入接口，以及在显示器的正确位置显示内容。

作为一个有点 n00b 到 Arduino 的程序，我确信这段代码可以改进。[我< 3 拉请求](https://github.com/oktadeveloper/okta-ardu-token-example)！

## 了解有关 Okta 验证和多因素认证的更多信息

我希望您喜欢看到使用 Okta Verify 的 MFA 认证如何与备用令牌设备一起工作。创建您自己的令牌的要求是一个带有时钟和显示器的可编程微处理器

如果你想更多地了解 Okta 的 MFA，看看这些帖子:

*   [MFA:开发者面临的 4 大挑战](https://developer.okta.com/blog/2018/05/16/multifactor-authentication-4-challenges-faced-by-developers)
*   [通过多重身份认证保护您的 Spring Boot 应用](https://developer.okta.com/blog/2018/06/12/mfa-in-spring-boot)
*   [从命令行使用多重因子](https://developer.okta.com/blog/2018/06/22/multi-factor-authentication-command-line)
*   [节点中的简单多因素认证](https://developer.okta.com/blog/2018/05/22/simple-multifactor-authentication-in-node)
*   [用 Okta API 设置和执行 MFA](https://developer.okta.com/blog/2018/02/08/set-up-and-enforce-multi-factor-auth-with-okta)

最后，请[在 Twitter](https://twitter.com/OktaDev) 上关注我们，寻找更多类似的优秀资源，请求我们撰写其他主题，并关注我们新的开源库和项目！

如果你喜欢这个项目，并且想在一个地方看到源代码，请去查看并启动它的 [GitHub 库](https://github.com/oktadeveloper/okta-ardu-token-example)。

还有…如果你有任何问题，请在下面留下评论！</video></video>