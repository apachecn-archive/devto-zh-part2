# 用道具类型验证 React 组件道具

> 原文：<https://dev.to/bnevilleoneill/validating-react-component-props-with-prop-types-186j>

#### 了解如何通过验证道具类型来改进 React 组件。

[![](img/9f558d512e304da1c00812abef04c4de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BYghaaXq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOokUL1A5kfzYkQjX5s9rRQ.jpeg)

Props 是一个非常重要的机制，用于将只读属性传递给 React 组件。这些属性通常需要具有特定的类型或形式，以便在组件中正确使用。

如果将一个属性以非必需的类型或形式传递给一个组件，该组件可能不会按预期方式运行。因此，改进 React 组件的一个好方法是 **props 验证**。

本指南假设您已经掌握了 React 的一些基本知识，并且是为已经使用 React 一段时间的开发人员准备的。

*不过，如果你还是 React 新手，可以从* [***这篇文档***](https://reactjs.org/docs/) *中了解更多关于 React 的内容。*

考虑这个代码片段:

```
import React from 'react';
import ReactDOM from 'react-dom';

function PercentageStat({ label, score = 0, total = Math.max(1, score) }) {
  return (
    <div>
      <h6>{ label }</h6>
      <span>{ Math.round(score / total * 100) }%</span>
    </div>
  )
}

function App() {
  return (
    <div>
      <h1>Male Population</h1>
      <div>
        <PercentageStat label="Class 1" total={360} score={203} />
        <PercentageStat label="Class 2" total={206} />
        <PercentageStat label="Class 3" score={107} />
        <PercentageStat label="Class 4" />
      </div>
    </div>
  )
}

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement); 
```

在这个代码片段中，创建了一个名为 PercentageStat 的组件，它需要 3 个道具才能正确呈现，即:标签、分数和总数。

在没有提供的情况下，为分数和总道具设置默认值。

最后，PercentageStat 在 App 组件中渲染了 4 次，每次使用不同的道具。

下面的截图显示了该应用程序的外观— *和一些引导样式*:

[![](img/c554be73ba086ecc36d4aa8eb0e6bfcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2UXYHG-Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AnB_CEQNguyAvqcrW.png) 

<figcaption>带 PercentageStat 组件的 App</figcaption>

注意，根据用法，label prop 应该是一个字符串。同样，分数和总计必须是数值，因为它们用于计算百分比。还要注意，total 不应该为 0，因为它被用作除数。

下面是另一个代码片段，展示了一个修改过的应用程序，它使用无效的属性来呈现 PercentageStat 组件。

```
function App() {
  return (
    <div>
      <h1>Male Population</h1>
      <div>
        <PercentageStat label="Class 1" total="0" score={203} />
        <PercentageStat label="Class 2" total={0} />
        <PercentageStat label="Class 3" score={f => f} />
        <PercentageStat label="Class 4" total={{}} score="0" />
      </div>
    </div>
  )
} 
```

下面的截图显示了应用程序视图现在的样子:

[![](img/21a99385978713286e9ad58b56b5adf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jddj9Gby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A5CZP-gVxykKir0N6.png) 

<figcaption>带有无效道具的 PercentageStat 组件</figcaption>

[![](img/86ffb999570e4efaceac3a814896185c.png)T2】](https://logrocket.com/signup/)

### 验证道具

正如上一节所演示的，验证组件属性的原因非常明显。可以使用一些技术来确保对 React 应用程序进行正确的类型检查和验证。

一个非常可行的选择是使用 JavaScript 扩展，如 [**Flow**](https://flow.org/) 或 [**TypeScript**](https://www.typescriptlang.org/) 来为整个应用程序添加类型检查。

#### 属性类型

React 提供了向组件添加类型检查的内部机制。React 组件使用一个名为 **propTypes** 的特殊属性来设置类型检查。

```
/**
 * FUNCTIONAL COMPONENTS
 */
function ReactComponent(props) {
  // ...implement render logic here
}

ReactComponent.propTypes = {
  // ...prop type definitions here
}

/**
 * CLASS COMPONENTS: METHOD 1
 */
class ReactComponent extends React.Component {
  // ...component class body here
}

ReactComponent.propTypes = {
  // ...prop type definitions here
}

/**
 * CLASS COMPONENTS: METHOD 2
 * Using the `static` class properties syntax
 */
class ReactComponent extends React.Component {
  // ...component class body here

  static propTypes = {
    // ...prop type definitions here
  }
} 
```

当属性被传递给一个 React 组件时，它们会根据在**属性中配置的类型定义进行检查。当为某个属性传递了无效值时，JavaScript 控制台上会显示一条警告。**

[![](img/8dcd01a9799f4d877bfa42f66f23750f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9_ca8kPE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ao5XE_YVvwxwrHAyQ.png) 

<figcaption>无效道具警告</figcaption>

如果为 React 组件设置了默认属性，则在根据属性类型进行类型检查之前，将首先解析这些值。因此，默认值也受属性类型定义的影响。

注意， **propTypes** 类型检查只发生在开发模式下，使您能够在开发时捕捉 React 应用程序中的 bug。出于性能原因，它不会在生产环境中触发。

#### 属性类型

在 React **15.5.0** 之前，React 包中提供了一个名为 **PropTypes** 的实用程序，它为配置组件属性的类型定义提供了许多验证器。它可以用 **React 访问。PropTypes** 。

然而，在 React 的更高版本中，这个工具被移到了一个名为 **prop-types** 的单独的包中。因此，您需要将它添加为项目的依赖项，以便访问 **PropTypes** 实用程序。

```
npm install prop-types --save 
```

它可以导入到您的项目文件中，如下所示:

```
import PropTypes from 'prop-types'; 
```

了解更多关于如何使用**道具类型**，它与使用**反应有何不同。PropTypes** 和所有可用的验证器，参见本文档[。](https://github.com/facebook/prop-types/blob/master/README.md)

### 可用的验证器

#### 基本类型

如前一节所述，PropTypes 实用程序导出了许多用于配置类型定义的验证器。以下是基本数据类型的验证器:

*   **`PropTypes.any`** —道具可以是任何数据类型
*   **`PropTypes.bool`** —道具应该是布尔型
*   **`PropTypes.number`** —道具应该是数字
*   **`PropTypes.string`** —道具应该是一根弦
*   **`PropTypes.func`** —道具应该是功能
*   **`PropTypes.array`** —道具应该是一个数组
*   **`PropTypes.object`** —道具应该是一个物体
*   **`PropTypes.symbol`** —道具应该是一个符号

```
Component.propTypes = {
  anyProp: PropTypes.any,
  booleanProp: PropTypes.bool,
  numberProp: PropTypes.number,
  stringProp: PropTypes.string,
  functionProp: PropTypes.func
} 
```

#### 可渲染类型

PropTypes 还导出以下验证器，以确保传递给 prop 的值可以由 React 呈现。

*   **`PropTypes.node`** —道具应该是可以由 React 渲染的任何东西:数字、字符串、元素或包含这些类型的数组(或片段)
*   **`PropTypes.element`** —道具应该是 React 元素

```
Component.propTypes = {
  nodeProp: PropTypes.node,
  elementProp: PropTypes.element
} 
```

**PropTypes.element** 验证器的一个常见用法是确保一个组件只有一个子组件。如果组件没有子组件或有多个子组件，JavaScript 控制台上会显示一条警告。

```
Component.propTypes = {
  children: PropTypes.element.isRequired
} 
```

#### 实例类型

如果您需要一个 prop 作为特定 JavaScript 类的实例，您可以使用验证器的 **PropTypes.instanceOf。这利用了底层 JavaScript **instanceof** 操作符。** 

```
Component.propTypes = {
  personProp: PropTypes.instanceOf(Person)
} 
```

#### 多种类型

PropTypes 还导出验证器，这些验证器允许一个属性的有限的一组值或多组数据类型。他们在这里:

*   **PropTypes.oneOf** —该属性限于一组指定的值，将其视为一个*枚举*
*   **PropTypes.oneOfType** —该属性应该是一组指定类型中的一个，其行为类似于类型的*联合*

```
Component.propTypes = {

  enumProp: PropTypes.oneOf([true, false, 0, 'Unknown']),

  unionProp: PropTypes.oneOfType([
    PropType.bool,
    PropType.number,
    PropType.string,
    PropType.instanceOf(Person)
  ])

} 
```

#### 收藏类型

除了 PropTypes.array 和 PropTypes.object 验证器之外，PropTypes 还提供了一些其他验证器，用于更好地验证数组和对象。

他们在这里:

的**prop types . array 可以用来确保 prop 是一个数组，其中所有的项都匹配指定的类型。** 

```
Component.propTypes = {

  peopleArrayProp: PropTypes.arrayOf(
    PropTypes.instanceOf(Person)
  ),

  multipleArrayProp: PropTypes.arrayOf(
    PropTypes.oneOfType([
      PropType.number,
      PropType.string
    ])
  )

} 
```

**PropTypes.objectOf** 可用于确保 prop 是一个所有属性值都匹配指定类型的对象。

```
Component.propTypes = {

  booleanObjectProp: PropTypes.objectOf(
    PropTypes.bool
  ),

  multipleObjectProp: PropTypes.objectOf(
    PropTypes.oneOfType([
      PropType.func,
      PropType.number,
      PropType.string,
      PropType.instanceOf(Person)
    ])
  )

} 
```

**PropTypes.shape** 可以在需要对对象属性进行更详细的验证时使用。它确保 prop 是一个包含一组具有指定类型值的指定键的对象。

```
Component.propTypes = {
  profileProp: PropTypes.shape({
    id: PropTypes.number,
    fullname: PropTypes.string,
    gender: PropTypes.oneOf(['M', 'F']),
    birthdate: PropTypes.instanceOf(Date),
    isAuthor: PropTypes.bool
  })
} 
```

对于*严格(或精确)*对象匹配，可以使用 **PropTypes.exact** 如下:

```
Component.propTypes = {
  subjectScoreProp: PropTypes.exact({
    subject: PropTypes.oneOf(['Maths', 'Arts', 'Science']),
    score: PropTypes.number
  })
} 
```

#### 所需类型

到目前为止看到的 PropTypes 验证器都允许 prop 是可选的。然而，您可以将 **isRequired** 链接到任何 prop validator，以确保在没有提供 prop 时显示警告。

```
Component.propTypes = {

  requiredAnyProp: PropTypes.any.isRequired,
  requiredFunctionProp: PropTypes.func.isRequired,
  requiredSingleElementProp: PropTypes.element.isRequired,
  requiredPersonProp: PropTypes.instanceOf(Person).isRequired,
  requiredEnumProp: PropTypes.oneOf(['Read', 'Write']).isRequired,

  requiredShapeObjectProp: PropTypes.shape({
    title: PropTypes.string.isRequired,
    date: PropTypes.instanceOf(Date).isRequired,
    isRecent: PropTypes.bool
  }).isRequired

} 
```

### 自定义验证器

大多数时候，您可能需要为组件属性定义一些定制的验证逻辑。例如，确保向道具传递有效的电子邮件地址。 **prop-types** 允许你定义可用于类型检查的自定义验证函数。

#### 基本自定义验证器

自定义验证函数有三个参数:

1.  **`props`** —包含传递给组件的所有属性的对象

2.  **`propName`** —待验证道具的名称

3.  **`componentName`** —组件的名称

如果验证失败，它应该返回一个错误对象。不应引发该错误。此外，console.warn 不应在自定义验证函数中使用。

```
 ![](htconst isEmail = function(props, propName, componentName) {
  const regex = /^((([^<>()[]\.,;:s@"]+(.[^<>()[]\.,;:s@"]+)*)|(".+"))@(([[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}])|(([a-zA-Z-0-9]+.)+[a-zA-Z]{2,})))?$/;

  if (!regex.test(props[propName])) {
    return new Error(`Invalid prop `${propName}` passed to `${componentName}`. Expected a valid email address.`);
  }
}

Component.propTypes = {
  email: isEmail,
  fullname: PropTypes.string,
  date: PropTypes.instanceOf(Date)
} 
```

[![](img/1e4724acbee807f7ecc14fa84bcc9a83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I0q6TqQt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ieagimnjojf7vz1x0cz.png) 

<figcaption>无效邮件道具警告</figcaption>

自定义验证函数也可以与 **PropTypes.oneOfType** 一起使用。下面是一个简单的例子，在前面的代码片段中使用了 **isEmail** 定制验证函数:

```
Component.propTypes = {
  email: PropTypes.oneOfType([
    isEmail,
    PropTypes.shape({
      address: isEmail
    })
  ])
} 
```

组件在这两种情况下都有效:

```
<Component email="glad@me.com" />
<Component email={{ address: 'glad@me.com' }} /> 
```

#### 自定义验证器和集合

自定义验证函数也可以与的**prop types . array 和**的**prop types . object 一起使用。以这种方式使用时，将为数组或对象中的每个键调用自定义验证函数。**

但是，自定义验证函数将采用 5 个 ***参数，而不是 3 个*** 。

1.  **`propValue`** —数组或对象本身

2.  **`key`** —迭代中当前项的键

3.  **`componentName`** —组件的名称

4.  **`location`** —被验证数据的位置。它通常是“道具”

5.  **`propFullName`** —当前正在验证的项目的完全解析名称。对于数组，这将是 array[index]。对于对象，这将是 object.key

下面是一个修改版的 **isEmail** 定制验证函数，用于集合类型:

```
const isEmail = function(propValue, key, componentName, location, propFullName) {
  const regex = /^((([^<>()[]\.,;:s@"]+(.[^<>()[]\.,;:s@"]+)*)|(".+"))@(([[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}])|(([a-zA-Z-0-9]+.)+[a-zA-Z]{2,})))?$/;

  if (!regex.test(propValue[key])) {
    return new Error(`Invalid prop `${propFullName}` passed to `${componentName}`. Expected a valid email address.`);
  }
}

Component.propTypes = {
  emails: PropTypes.arrayOf(isEmail)
} 
```

#### 通用自定义验证器

考虑到您已经学习的关于自定义验证函数的所有知识，您可以继续创建通用的自定义验证器，它可以用作独立的验证器，也可以用于集合类型。

对 **isEmail** 定制验证函数稍加修改，它将成为一个通用的验证器，如下面的代码片段所示。

```
const isEmail = function(propValue, key, componentName, location, propFullName) {
  // Get the resolved prop name based on the validator usage
  const prop = (location && propFullName) ? propFullName : key;

  const regex = /^((([^<>()[]\.,;:s@"]+(.[^<>()[]\.,;:s@"]+)*)|(".+"))@(([[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}])|(([a-zA-Z-0-9]+.)+[a-zA-Z]{2,})))?$/;

  if (!regex.test(propValue[key])) {
    return new Error(`Invalid prop `${prop}` passed to `${componentName}`. Expected a valid email address.`);
  }
}

Component.propTypes = {
  email: PropTypes.oneOfType([
    isEmail,
    PropTypes.shape({
      address: isEmail
    })
  ]),
  emails: PropTypes.arrayOf(isEmail)
} 
```

### 验证 PercentageStat

作为本指南的总结，下面的代码片段将属性类型添加到开头部分的 **PercentageStat** 组件中。

```
import React from 'react';
import PropTypes from 'prop-types';

// The PercentageStat component
function PercentageStat({ label, score = 0, total = Math.max(1, score) }) {
  return (
    <div>
      <h6>{ label }</h6>
      <span>{ Math.round(score / total * 100) }%</span>
    </div>
  )
}

// Checks if a value is numeric
// Either a finite number or a numeric string
function isNumeric(value) {
  const regex = /^(\+|-)?((\d*\.?\d+)|(\d+\.?\d*))$/;
  return Number.isFinite(value) || ((typeof value === "string") && regex.test(value));
}

// Checks if value is non-zero
// Value is first converted to a number
function isNonZero(value) {
  return +value !== 0;
}

// Takes test functions as arguments and returns a custom validation function.
// Each function passed in as argument is expected to take a value argument is
// expected to accept a value and return a boolean if it passes the validation.
// All tests must pass for the custom validator to be marked as passed.
function validatedType(...validators) {
  return function(props, propName, componentName) {

    const value = props[propName];

    const valid = validators.every(validator => {
      if (typeof validator === "function") {
        const result = validator(value);
        return (typeof result === "boolean") && result;
      }

      return false;
    });

    if (!valid) {
      return new Error(`Invalid prop \`${propName}\` passed to \`${componentName}\`. Validation failed.`);
    }

  }
}

// Set the propTypes for the component
PercentageStat.propTypes = {
  label: PropTypes.string.isRequired,
  score: validatedType(isNumeric),
  total: validatedType(isNumeric, isNonZero)
} 
```

### 结论

在本指南中，我们已经看到了如何使用适当类型来改进您的 React 组件，并确保它们按预期使用。

如果你想了解更多关于在 React 中验证组件属性的信息，你可以查看本指南。

#### 拍拍&跟着

如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/u/ddcd0e9719e5) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

***享受编码……***

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

用道具类型验证 React 组件道具的帖子[最先出现在](https://blog.logrocket.com/validating-react-component-props-with-prop-types-ef14b29963fc/)[日志火箭博客](https://blog.logrocket.com)上。