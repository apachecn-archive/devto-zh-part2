# 使用 Vanna 的 JavaScript 中的特性标志

> 原文：<https://dev.to/acceldelivery/feature-flags-in-javascript-withvanna-20ng>

## 我看到一个红色按钮

Vanna 是一个开源特性标记库，在 [PBS](https://www.pbs.org) 编写和使用。让我们深入研究一下 [JavaScript 客户端](https://github.com/pbs/vanna-js-client)。来设置我们的教程，一个故事。

Mick 是前端开发人员。设计团队要求 Mick 将一个红色按钮的颜色改为黑色。产品管理还没有准备好全力以赴。设计和产品管理部门询问我们挥霍的工程师是否有办法来对冲我们的赌注。他们想向一小群用户展示实验性的黑色按钮。米克微笑着戴上墨镜。😎

这里有一个简单的例子来说明 Vanna 是如何让你做到这一点的:

```
// 👇 An instance of vanna client - implementation to come
import features from "app/features"; 

const paintItBlack = features.variation("paint-it-black")

if (paintItBlack) {
  // Render experimental black button
} else {
  // Render red button
} 
```

Enter fullscreen mode Exit fullscreen mode

这是最简单的特征标记。通过特征标记，您可以更频繁地将[合并到主干](https://accelerate.delivery/trunk-development-feature-flags/)中。您可以通过将新的、易变的代码限制在一部分用户中来降低风险。Vanna 让您可以在应用程序代码之外控制这一点。这开启了持续交付的另一个特点。

连续交付的一个理想目标是从发布中分离部署。部署是将代码移动到服务器的行为。发布是向用户提供代码路径的行为。你可以在[这篇黑客午间文章](https://hackernoon.com/decouple-deployment-from-release-b4b9182b6a46)中了解更多。为了从部署中分离发布，Vanna 从 JSON 响应中接收其特性。这允许我们在不进行代码部署的情况下更新特性可用性。

## 创建特征

让我们深入了解特性响应的形状。响应如下:

```
{
  "features": {
    "paint-it-black": {
      "slug": "i-want-to-paint-it-black",
      "enabled": true,
      "targetSegment": ["alpha-tester"]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

特征响应包含任意数量的特征对象。在我们的示例
中，有一个特征`"paint-it-black"`。该特性有三个
属性:

*   `"slug"` -为特征命名。当只给出特征值时，它对特征识别很有用。在我们的高级示例中，我们将使用它来覆盖特性可用性。
*   `"enabled"` -该键使该功能可用。把它想象成主断路器。如果这是`false`，该功能将对所有人关闭。
*   `"targetSegment"` -功能目标用户。您可以使用此密钥为用户组提供一项功能。我们将看到当我们实例化一个`new VannaClient`时，用户如何识别为一个`userSegment`。

目前没有控制台界面来创建这个 JSON 响应。现在我们将手动编写 JSON，并通过 CDN 访问它。创建该响应的管理界面和 API 服务是未来的增强功能。手工制作 JSON 是我们在开发 Vanna 库的过程中迈出的最小的一步。采用这种 MVP 方法使我们更容易试验和迭代。

## 使用 vanna-js

在我们的简单示例中，我们假设客户端库是可用的。我们来实施吧。

我们将基于 cookie 的存在来设置`userSegment`。参见我们之前的[帖子](https://accelerate.delivery/setting-cookie-flags-django/)关于为功能标志设置 cookies。

```
// app/features.js
import { VannaClient } from "@pbs/vanna";
import Cookies from "js-cookie";

const isAlphaTester = Cookies.get("alpha-tester");

const client = new VannaClient({                                              
  uri: "https://cdn.com/features.json",                    
  userSegment:  isAlphaTester ? "alpha-tester" : "regular",           
  fallbacks: {                                                                
    "paint-it-black": false                                              
  }                                                                          
}); 
```

Enter fullscreen mode Exit fullscreen mode

当您实例化一个`new VannaClient`时，您负责:

*   `uri` -这是 JSON 特性控制响应的位置。
*   `userSegment` -这是用户组。Vanna 在与已启用的`"targetSegment"`匹配时为该用户启用该功能。
*   `fallbacks` -设置特征标志的默认行为。注意，必须为 JSON 响应中的每个特性设置回退。

我们现在可以使用 Vanna 来完成我们的任务。在我们最初的例子中，我们创建了一个布尔值来分割我们的代码路径，用:

```
const paintItBlack = features.variation("paint-it-black") 
```

Enter fullscreen mode Exit fullscreen mode

Vanna 的`variation()`方法考虑了特征的`"targetSegment"`和客户端的`userSegment`。在两者匹配时，该方法返回`true`。

通过本教程，您可以将 Vanna 用作特征标记库。您可以将部署与发布分离。您可以以更低的风险更快地交付软件。以这种方式使用 Vanna 进行特性标记非常适合简单的用例。需要更多定制的高级用户可以使用高级选项。

## 压倒变化

用一个`userSegment`来控制特性似乎有些粗糙。如果我们想要更好的控制呢？如果不管我的`userSegment`如何，我都想启用一个特定的功能怎么办？Vanna 客户端允许您覆盖变体资格。我们可以扩展我们之前的[帖子](https://accelerate.delivery/toggle-django-views-with-cookie-flags/)关于切换特定功能 cookies 上的标志。我们将允许 Vanna 基于命名 cookies 的存在选择加入一个特性。以下突出显示的模块显示了我们如何添加到我们以前的 Vanna 客户端:

```
// app/features.js
import _ from "lodash";
import { VannaClient, getFeatureVariation } from "@pbs/vanna";
import Cookies from "js-cookie";

function getVariationOverride(featureSlug) {
  const featureKey = `feature:${featureSlug}`;
  const overrideValue = Cookies.get(featureKey);
  if (overrideValue) {
    return overrideValue === "true";
  }
  return undefined;
}

const isAlphaTester = Cookies.get("alpha-tester");

const client = new VannaClient({                                              
  uri: "https://cdn.com/features.json",                    
  userSegment:  isAlphaTester ? "alpha-tester" : "regular",           
  fallbacks: {                                                                
    "paint-it-black": false                                              
  },
  _overrides: {
    getFeatureVariation: (feature, { userSegment }) => {
      const variation = getFeatureVariation(feature, { userSegment });
      const overrideVariation = getVariationOverride(feature.slug);
      return _.isUndefined(overrideVariation) ? variation : overrideVariation;
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

有了这些额外的代码，用户可以选择加入不属于他们`userSegment`的功能。在我们的例子中，如果用户没有`"alpha-tester"` cookie，但是有一个`"feature:i-want-to-paint-it-black"` cookie，他们将会看到黑色按钮。相反的用例也适用。一个`"alpha-tester"`可以通过将一个命名的 cookie 设置为`"false"`来退出一个特性。这种变化覆盖允许对功能可用性进行更精细的控制。我们使用 cookies 来覆盖这种变化，但是您可以使用本地存储或 JavaScript 中可用的任何东西。

vanna-js-client 是一个开源项目。请查看简单易读的源代码。这是一种向 JS 项目添加特性标志的轻量级方法。