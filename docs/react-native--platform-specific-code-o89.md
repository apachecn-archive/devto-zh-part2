# 反应本机—特定于平台的代码

> 原文：<https://dev.to/nedimb86/react-native--platform-specific-code-o89>

有了 React Native，我们正在为 iOS 和 Android 编写代码。很快就会注意到我们需要彼此不同。

[![](img/7c6fd336b9d4ef03703f31957ac2bfac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EVZwx5mZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/353/1%2A27A-seCkORB2P4QyEWIZUQ.png)

[![](img/5fad6bb81e89330917fff5b68e8e311f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8p_J-3KU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/405/1%2Azsngww4SLJp5W5CcwD2xvg.png)

[![](img/968c0236644ff44d4bb2da3d7da9e38b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WQdA1-cn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/274/1%2Aq6svgsA7XjoEX1t2zUw-BQ.png) 

<figcaption>右:安卓模拟器 Nexus 5 API 23 1080 x 1920:xxhdpi x86 _ 64</figcaption>

正如我们所看到的，我们的 *Header* 组件，具有显示文本的简单任务，在 Android 和 iOS 上表现不同。显然，我们需要两种不同的风格。那么，我们如何才能做到这一点呢？React 原生团队的优秀人员为我们提供了一个非常简单的解决方案。他们为我们提供了一个叫做 ***平台*** 的模块。

我们所需要做的就是从 *react-native* 中导入*平台*，我们就可以开始了。*平台*有 ***OS*** 属性，这告诉我们要么我们在 ***iOS (ios)*** 或***Android(Android)***上运行我们的应用。此外，*平台*带有一个*选择*的方法，该方法给出了一个带有*平台键的对象。OS* 将返回我们运行代码的平台的值。

说够了。让我们看看那些在行动中。

```
import React from 'react';
import { View, Text, StyleSheet, Platform } from 'react-native';

export const Header = () => (
  <View style={styles.header}>
    <Text style={styles.text}>I am Header</Text>
  </View>
);

const styles = StyleSheet.create({
  header: {
    height: Platform.OS === 'android' ? 76 : 100,
    marginTop: Platform.OS === 'ios' ? 0 : 24,
    ...Platform.select({
      ios: { backgroundColor: '#f00', paddingTop: 24},
      android: { backgroundColor: '#00f'}
    }),
    alignItems: 'center',
    justifyContent: 'center'
  },
  text: {
    color: '#fff',
    fontSize: 24
  }
}); 
```

运行这些代码的结果是:

[![](img/908a524c1f524f22d15e936d28be0097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NdDVzuSn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/558/1%2AF4oppM9YhvpThAadRb9LDQ.png) 

<figcaption>页眉样式应用后</figcaption>

让我们分解我们的代码！

```
height: Platform.OS === 'android' ? 76 : 100,
marginTop: Platform.OS === 'ios' ? 0 : 24, 
```

这里没什么特别的。我们已经提到过*平台。OS* 返回 *ios* 或 *android* 取决于它运行的平台。结合三元运算符，我们得到了这段漂亮的代码，它帮助我们设置了*标题*的高度/边距。这段代码相当于 Android 上的`height: 76, marginTop: 24`和 iOS 上的`height:100, marginTop: 0`

继续前进，我们有:

```
...Platform.select({
 ios: { backgroundColor: '#f00', paddingTop: 24},
 android: { backgroundColor: '#00f'}
 }), 
```

正如我们提到的*平台，select* 将返回来自*平台的键值。OS* 。所以在我们的例子中，这个代码在 iOS 上变成了`...{ backgroundColor: '#f00', paddingTop: 24}`，在 Android 上变成了`...{ backgroundColor: '#00f'}`。

所以总结一下，或者说风格会是这样的:

```
Android:
const styles = StyleSheet.create({
 header: {
 height: 76,
 marginTop: 24,
 backgroundColor: '#00f',
 alignItems: 'center',
 justifyContent: 'center'
 },
 text: {
 color: '#fff',
 fontSize: 24
 }
});

-----------------------------------

iOS:
const styles = StyleSheet.create({
 header: {
 height: 100,
 marginTop: 0,
 backgroundColor: '#f00', 
 paddingTop: 24,
 alignItems: 'center',
 justifyContent: 'center'
 },
 text: {
 color: '#fff',
 fontSize: 24
 }
}); 
```

我们还没有结束*平台。选择*。它的酷之处在于它接受任何值，因此您可以利用这一点来返回 iOS/Android 的组件。在我们的例子中，我们已经创建了 *BodyAndroid.js、BodyIOS.js 和 Body.js* 。我们已经用*主体*组件替换了 *App.js* 中的默认文本。所以，我们的 *App.js* 看起来是这样的:

```
import React from 'react';
import { View} from 'react-native';
import {styles} from "./src/theme/Style";
import { Header } from './src/components/Header';
import { Body } from "./src/components/Body";

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Header />
        <Body />
      </View>
    );
  }
} 
```

我们代码的其余部分:

```
BodyAndroid.js
import React from 'react';
import { View, Text} from 'react-native';
import {styles} from "../theme/Style";

export const BodyAndroid = () => (
  <View style={styles.body}>
    <Text style={styles.h1}>This is Android App!</Text>
  </View>
);
--------------------------------
BodyIOS.js
import React from 'react';
import { View, Text} from 'react-native';
import {styles} from "../theme/Style";

export const BodyIOS = () => (
  <View style={styles.body}>
    <Text style={styles.h1}>This is iOS App!</Text>
  </View>
);
--------------------------------
Body.js
import { Platform } from 'react-native';

import { BodyAndroid } from './BodyAndroid';
import { BodyIOS } from './BodyIOS'

export const Body = Platform.select({
  ios: BodyIOS,
  android: BodyAndroid
}); 
```

结果是:

[![](img/b29e097859bfd42a4957cb19c4061e76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hA8qlbRo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/510/1%2A-jQkLNSm62SMjnpSB_zOQg.png)

虽然看起来不错，但我不认为这是最好的解决办法。有一种叫做 ***的平台专用扩展*** 我比较喜欢。因此，在 iOS 的情况下，我们希望有`.ios.`扩展，而对于 Android，我们将有`.android.`。这将有助于 React Native 确定哪个平台使用哪个组件。

让我们用一个例子来说明这一点。

[![](img/902c97c096ab24491b053ab81ec6eaa9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qMLYM2F7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/353/1%2AbNqv2IwY33y28DWwCw_WlQ.png)

[![](img/a7d3665e4e1a345e6c1602d0aa0a915e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IimrKAv4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/660/1%2A0D5GGp57xaqUKxfZTbgRPg.png)

[![](img/1f3fe27b487c09d261e7998182881ec5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SPr4nBkt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/274/1%2AvOY86XGPoRv2OveCzL3MjQ.png)

我们的*页脚*的代码与我们的*页眉*非常相似，因此，我不会在这里粘贴它。对我们来说重要的是这条简单的线`import { Footer } from './src/components/Footer';`。正如我们可以注意到的，在组件目录中，我们没有页脚文件，但是有右边的 *Footer.ios* 和 *Footer.android* 。React Native 足够智能，可以根据我们构建应用的平台来决定使用哪一个。

我们已经看到了如何使用特定于*平台的*扩展和*平台来添加我们的组件。这就是*平台模块*所能做的一切吗？哦，不。还有一件事，你可能已经猜到了。正在检测 Android/iOS 的版本。因此，让我们修改我们的正文消息，向它附加版本号。*

[![](img/07a608220afaa1a5b8447253ed8bee84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NCIXdySv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/528/1%2AJ-WyBmyljd0UgkcHo7UEvQ.png)

显示*平台*版本就像调用**平台*一样简单。版本*** 。嗯……有一个条件。Android 以整数的形式返回版本，而 iOS 就不那么友好了，它会以字符串的形式返回版本。如果我们需要比较它，将它转换成整数应该不难(如果我们需要版本号，这是最有可能的情况)。如果我们只是想展示它，我们可以放心地使用我们得到的东西。

为了让一切更有趣，React Native 附带了内置组件和 API，其中一些是 Android/iOS 特定的。举几个，对于 Android 我们有:*datepickrandroid，ProgressBarAndroid，view page Android*；至于 iOS 我们有: *AlertIOS，ImagePickerIOS，TabBarIOS* 。此外，我们可以为我们的平台编写本地模块，但这是我们自己的话题。

荣誉提及去组件参数。有些组件具有特定于平台的参数，我们将在提到每个组件或组件集时涉及这些参数。

### 结论

上平台还是不上平台，这是个难题。当涉及本机反应时，特定于平台的代码的使用非常简单。正如我们所看到的，我们有各种各样的选择。要么创建特定于平台组件，要么通过确定 OS 来修改组件。所以这完全取决于我们，以及我们的情况。

本文中使用的代码可以在以下位置找到:

[nedimb86/RNPlatform](https://github.com/nedimb86/RNPlatform)

* * *