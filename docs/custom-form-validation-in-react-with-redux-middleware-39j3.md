# React 与 Redux 中间件中的自定义表单验证

> 原文：<https://dev.to/sophiedebenedetto/custom-form-validation-in-react-with-redux-middleware-39j3>

*这篇文章最初发表在 [TuneCore 技术博客](http://tech.tunecore.com/)*
Redux 为国家管理提供了一个干净的架构。那么，为什么我们继续用复杂的验证逻辑来混淆我们的组件呢？相反，让我们利用 Redux，在一些定制中间件的帮助下！

## Redux 和状态管理

Redux 为我们的 React 应用程序提供了一个集中的状态管理系统。我们将我们的组件树订阅到一个中央存储中，并且通过一个数据向下操作向上的模式来实现状态更改。动作被分派给存储，存储使用一个 reducer 来改变状态并将新状态广播给我们的组件，然后组件重新呈现。

让 Redux 管理我们的 React 应用程序的状态意味着从单个组件——甚至是我们的大型容器组件——中拿走(大部分)职责。我们不会让我们的组件建立复杂的内部状态，也不会用复杂的逻辑来加重这些组件的负担，以更新这样的状态。相反，我们使用 Redux 存储来塑造应用程序的状态；动作创建器功能传达状态改变的需要；进行状态改变的减速器。那么，我们为什么要区别对待表单验证和错误处理呢？

尽管如此多的 React 开发人员坚持 Redux 架构，但仍然经常看到复杂的表单组件处理自己的验证和错误。让我们允许 Redux 做它最擅长的事情，并为我们管理这样的交互！

## App

***注**:你可以在 GitHub [这里](https://github.com/SophieDeBenedetto/spacex-apply)查看这个项目的完整代码，你还可以在这里玩一下现场演示[。请记住，这是一个简单的虚拟应用程序，因此没有持久层。抱歉，我们并没有真正保存您的表单
回复:(](https://spacex-apply.herokuapp.com/)*

你可能已经听说我们现在可以去太空旅行了。埃隆·马斯克正在寻找火星任务的工作人员。所有世界顶尖的宇航员和书呆子都在竞争飞船上的一个位置。为了申请一个职位，你必须填写一份相当复杂、严谨的申请表。作为表单的开发者，我们需要实现一组复杂的表单验证。

下面是我们要追求的行为:

[https://www.youtube.com/embed/O9aBHizDpvo](https://www.youtube.com/embed/O9aBHizDpvo)

我们的表单验证范围从标准:

*   如果没有必填的姓名和电子邮件字段，则无法提交表单。
*   电子邮件必须是格式正确的电子邮件地址。

到更复杂的:

*   用户提供的电子邮件*必须*是他们的官方 spacex 电子邮件地址–`name@space.ex`——因为只有注册的 spacex 成员才能申请这项任务。
*   如果申请人检查他们是否有改造其他星球的经验，他们必须填写“你改造过哪些星球？”文本字段。
*   “你改造了哪些星球？”文本字段*不能包含“火星”——这是一项火星任务，我们知道你还没有将其地球化！*

我们可以想象复杂表单验证的列表可以一直持续下去。试图在一个组件中管理所有这些，比如说一个`FormContainer`组件，会变得非常混乱，非常快。相反，我们将把表单验证和错误消息的填充卸载给 Redux。

### 应用状态

我们的应用程序非常简单——它显示一个宇航员申请表并提交该表。我们的初始状态是这样的:

```
// client/src/store/initialStates/astronaut.js

{
  astronaut: {
    id: null,
    name: "",
    email: "",
    terraform_experience: false,
    terraform_planets: ""
  }
} 
```

### 组件树

我们的组件架构也很简单。我们有一个顶级容器组件:`AstronautForm`，它包含一些子组件，每个子组件代表表单的一部分。

下面是一个简化的外观:

[`client/src/components/AstronautForm.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/components/AstronautForm.js)T3】

```
import React                  from 'react';
import { Form, Button}        from 'react-bootstrap'
import { connect }            from 'react-redux';
import { bindActionCreators } from 'redux';
import * as astronautActions  from '../actions/astronautActions';
import AstronautName          from './form/AstronautName';
import AstronautEmail         from './form/AstronautEmail';
import TerraformExperience    from './form/TerraformExperience';
import TerraformPlanets       from './form/TerraformPlanets';

class AstronautForm extends React.Component {
  ...
  render() {
    const {
      id,
      name,
      email,
      terraform_planets,
      terraform_experience
    } = this.props.astronaut;

    return (
      <Form key="astronaut-form" onSubmit={this.submitForm}>
        <AstronautName
          name={name}
          onAttributeUpdate={this.updateAstronautAttributes}/>
        <AstronautEmail
          email={email}
          onAttributeUpdate={this.updateAstronautAttributes}/>
        <TerraformExperience
          terraformExperience={terraform_experience}
          onAttributeUpdate={this.updateAstronautAttributes}/>
        <TerraformPlanets
          terraformExperience={terraform_experience}
          terraformPlanets={terraform_planets}
          onAttributeUpdate={this.updateAstronautAttributes}/>
        <Button type="submit">
          Submit
        </Button>
        <Button onClick={this.clearForm}>
          Clear
        </Button
      </Form>
    )
  }
}

function mapStateToProps(storeState, componentProps) {
  const { astronaut } = storeState;
  return { astronaut };
}

function mapDispatchToProps(dispatch) {
  return { actions: bindActionCreators(astronautActions, dispatch) }
};

export default connect(mapStateToProps, mapDispatchToProps)(AstronautForm); 
```

我们的`AstronautForm`组件是容器组件。它连接到 Redux 并知道状态变化。它使用`mapStateToProps`将`astronaut`从状态中取出，并使其成为组件的`props`的一部分。它*包含*(懂了吗？)组成我们表单的子组件:

*   `AstronautName`:表单上的姓名字段
*   我们表单上的电子邮件字段
*   `TerraformExperience`:地形改造体验复选框
*   `TerraformPlanets`:地球化行星文本域

### 用动作和还原器管理状态

我们的 Redux 架构处理宇航员状态属性的更新:姓名、电子邮件、terraform 体验和 terraform 行星。

当用户填写完一个特定的表单字段时，我们使用`onBlur`事件来调度一个更新 state 中相应属性的动作。

让我们以`AstronautName`组件为例来看看:

[`client/src/components/form/AstronautName.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/components/form/AstronautName.js)T3】

```
import React from 'react';

class AstronautName extends React.Component {
  state = {
    name: ""
  };

  componentWillReceiveProps(nextProps) {
    this.setState({name: nextProps.name});
  };

  onChange = (e) => {
    this.setState({name: e.target.value});
  };

  onBlur = (e) => {
    this.props.onAttributeUpdate(
      { name: this.state.name }
    )
  };

  render() {
    const { name } = this.state;
    return (
      <div>
        <label>Name</label>
        <input
          type="text"
          onBlur={this.onBlur}
          onChange={this.onChange}
          value={name}/>
      </div>
    )
  }
};

export default AstronautName; 
```

我们从`AstronautForm`父组件传入`name`作为道具。我们使用`componentWillReceiveProps`将它放入`AstronautName`的内部状态。

我们使用`onChange`事件用更新后的名称更新`AstronautName`的状态。我们使用`onBlur`事件来调用`onAttributeUpdate`函数。

该函数作为`props`的一部分从`AstronautForm`传入。`AstronautForm`这样定义函数:

[`client/src/components/AstronautForm.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/components/AstronautForm.js)T3】

```
...
updateAstronautAttributes = (newAttributes) => {
  this.props.actions.updateAstronautAttributes(newAttributes)
}; 
```

我们分派了一个动作创建函数`updateAstronautAttributes`。我们的操作看起来像这样:

[`client/src/actions/astronautActions.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/actions/astronautActions.js)T3】

```
export function updateAstronautAttributes(newAttributes) {
  return {
    type: "UPDATE_ASTRONAUT_ATTRIBUTES",
    newAttributes
  }
} 
```

这个动作是由我们的`astronautReducer`这样处理的:

[`client/src/reducers/astronautReducer.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/reducers/astronautReducer.js)T3】

```
import defaultState from '../store/initialStates/astronaut.js'

export default function astronautReducer(state=defaultState, action) {
  switch(action.type) {
    case "UPDATE_ASTRONAUT_ATTRIBUTES":
      return {...state, ...action.newAttributes}
    ...
  }
} 
```

这创建了应用程序中央状态的新版本，相应地更新了我们的组件。

### 提交表单

当用户点击表单上的“提交”按钮时，我们触发在`AstronautForm`容器组件中定义的`submitForm`函数:

[`client/src/components/AstronautForm.js`:](////%20src/components/AstronautForm.js)T3】

```
...
submitForm = (e) => {
  e.preventDefault();
  this.props.actions.saveAstronaut(this.props.astronaut);
}; 
```

如前一节所述，每次用户触发特定表单字段(姓名、电子邮件、地球化体验、地球化星球)的`onBlur`事件时，我们都会调度一个动作来更新应用程序状态中的相应属性。由于`AstronautForm`组件是通过`connect`函数连接到 Redux 的，所以每次出现这样的状态变化，组件都会重新渲染，并调用`mapStateToProps`。从而确保在任何给定的时间点，当用户点击“提交”时，在`this.props.astronaut`的宇航员是最新的变化。

所以，我们的`submitForm`函数只需要用一个参数`this.props.astronaut`来调度`saveAstronaut`动作创建函数。

我们的`saveAstronaut`动作需要向我们的 API 发送一个 web 请求来提交表单。我们知道，没有中间件的帮助，我们不能将一些异步代码放入动作创建器函数的中间。因此，我们有一些定制的 API 中间件来为我们发送 web 请求。如果你对定制异步中间件不熟悉，我强烈推荐查看官方 Redux 中间件文档，以及[我的 TuneCore 队友 Charlie Massry 写的这篇优秀的文章](http://tech.tunecore.com/redux-API-middleware)。

我们的操作看起来像这样:

[`client/src/actions/astronautActions.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/actions/astronautActions.js)T3】

```
export function saveAstronaut(astronaut) {
  return {
    type: "API",
    astronaut
  };
} 
```

我们的中间件看起来像这样:

[`client/src/middleware/apiMiddleware.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/middleware/apiMiddleware.js)T3】

```
import {
  saveAstronautSuccess,
  saveAstronautFailure
} from '../actions/astronautActions';

const apiMiddleware = ({ dispatch, getState}) => next => action => {
  if (action.type !== "API") {
    return next(action)
  }
  fetch('/api/astronauts', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      astronaut: action.astronaut
    })
  }).then((response) => {
    return response.json();
  }).catch((error) => {
    dispatch(saveAstronautFailure(error));
  }).then((data) => {
    dispatch(saveAstronautSuccess(data));
  });
};

export default apiMiddleware; 
```

我们的中间件在发送动作创建器函数的返回值给缩减器之前被商店*调用。如果动作的类型是`"API"`，我们将使用`fetch`来发送我们的 API 请求。然后，当承诺解决时，我们将调度另一个动作。出于本文的目的，我们不会担心我们的成功和失败函数。可以说，`success`函数用被救宇航员更新状态，`failure`函数用一些错误消息更新状态。*

既然我们已经理解了 React + Redux 应用程序的整体结构，我们就可以开始处理表单验证了。

### 表单验证

为了让我们的应用程序按预期工作，我们必须处理三类表单验证。

*   必填字段(如姓名和电子邮件)
*   提交表单时需要运行的自定义验证
*   在状态中更新属性时需要运行的自定义验证

让我们从最容易得到的结果开始:必填字段。

### 必填字段:简易 HTML5 验证

创建一个必填字段，从而防止用户在没有该字段的情况下提交表单，只用 HTML 就可以非常容易地做到。我们简单地将`required`添加到输入标签中。

[`client/src/components/form/AstronautName.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/form/AstronautName.js)T3】

```
...
render() {
  const { name } = this.state;
  return (
    <div>
      <label>Name</label>
      <input
        required
        type="text"
        onBlur={this.onBlur}
        onChange={this.onChange}
        value={name}/>
    </div>
  )
} 
```

现在，当用户没有填写该字段就点击“提交”时，我们会看到这样的行为:

[https://www.youtube.com/embed/hgBP8gEPess](https://www.youtube.com/embed/hgBP8gEPess)

布拉莫。

我们可以为我们的电子邮件字段做同样的事情来达到同样的效果。

### 提交时验证

让我们继续讨论一些更复杂的表单验证。如果用户点击复选框，表明他们*确实*有改造其他星球的经验，我们想要求他们填写“你改造过哪些星球？”文本字段。

我们*不能*验证`terraformed_experience`复选框模糊时`terraformed_planets`的存在。这将导致在用户点击复选框后，在用户有机会与`terraform_planets`文本字段交互之前，terraformed planets 字段*显示错误。*

我们*可以*(并且应该)验证`terraform_planets`文本字段的模糊性。但是如果用户根本没有点击那个字段呢？如果他们勾选了`terraform_experience`复选框，然后立即点击“提交”会怎么样。在这种情况下，我们*不想*将表单提交给 API。我们希望在发送 web 请求之前执行此验证。

### 为什么我们不应该在组件中进行验证

我们可以通过在`AstronautForm`中向我们的`submitForm`函数添加代码来直接在组件中处理这个问题:

#### 坏榜样，不要这样:

```
submitForm = (e) => {
  e.preventDefault();
  if (this.props.astronaut.terraform_experience && !this.props.astronaut_planets {
    this.props.actions.saveAstronaut(this.props.astronaut);
  } else {
    this.setState({
      errors:
        ...this.state.errors,
        terraform_planets: true
      }
    }
  }
}; 
```

这种方法有一些缺点。

*   它要求我们将`errors`存储在`AstronautForm`组件的状态中。虽然这本身没有任何问题，但是在单个组件中存储复杂状态正是 Redux 允许我们避免的。
*   我们开始向组件中添加复杂的逻辑。目前，我们只关注两个属性。但是如果我们真的希望我们的组件处理这种验证，这段代码将不得不增长到验证每一个宇航员属性。这不仅很麻烦，而且还迫使表单组件的提交功能暴露了它的职责。它不再简单地提交表单，现在它验证存储在 props 中的宇航员对象，并由*决定*是否应该提交表单或更新组件的内部状态。把你的表单提交功能想象成一个你不完全信任的弟弟妹妹，不会把很多责任交给他(无意冒犯 Zoe)。我们的表单提交功能应该做到这一点--提交表单。它*不应该*负责验证宇航员或者更新状态。

让 Redux 来处理验证宇航员*和*跟踪宇航员的错误。

### 在 Redux 状态下跟踪错误

当我们第一次建立我们的国家时，我们建立了一个看起来像这样的物体:

[`client/src/store/initialStates/astronaut.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/store/initialStates/astronaut.js)T3】

```
{
  astronaut: {
    id: null,
    name: "",
    email: "",
    terraform_experience: false,
    terraform_planets: ""
  }
} 
```

让我们扩展 state 的`astronaut`键来包含错误，为我们想要验证的每个属性跟踪一个错误:

```
{
  astronaut: {
    id: null,
    name: "",
    email: "",
    terraform_experience: false,
    terraform_planets: "",
    errors: {
      name: null,
      email: null,
      terraform_planets: null
    }
  }
} 
```

既然 Redux 状态中的`astronaut`键包含了自己的错误，我们可以依靠我们的`astronautReducer`来适当地更新这些错误。我们什么时候告诉我们的缩减器更新宇航员的错误？让我们回到我们的用例:“提交时验证”。

### 自定义验证中间件

根据我们之前的例子，我们知道当用户提交表单时，我们希望验证是否存在`terraform_planets`，*，如果他们已经选中了`terraform_experience`框*。

我们希望在用户点击提交*后*执行验证，而不是在组件内*执行验证，并且我们希望在*API 请求被发送前*执行验证。如果宇航员无效，我们*不想*发送 API 请求。相反，我们将分派一个动作，告诉我们的 reducer 更新状态中适当的错误。*

我们究竟如何才能插入在提交表单和发送`saveAstronaut`动作之后的时间点*，而不是在*发送 API 请求之前的时间点*？当然是定制中间件！*

我们将定义一些自定义验证中间件，并在自定义 API 中间件之前将其添加到我们的中间件堆栈中。这样，它将在 API 中间件被调用之前被调用，即在 API 请求被发送之前。

这个图表说明了我们的中间件在 Redux 生命周期中的位置。

[![custom middleware](img/0301433d4435687d01f9d3d8cf1672ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6LBfOqjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2018/02/custom-middleware--1-.svg)

#### 定义中间件

我们将定义表单验证中间件:

[`client/src/middleware/formValidationMiddleware.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/middleware/formValidationMiddleware.js)T3】

```
const formValidationMiddleware = ({ dispatch, getState}) => next => action => {
  // validations coming soon!
};

export default formValidationMiddleware; 
```

#### 添加到中间件堆栈

我们将在自定义`apiMiddleware`之前将其添加到堆栈*。*

[`client/src/store/configureStore.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/store/configureStore.js)T3】

```
import {
  createStore,
  applyMiddleware } from 'redux'
import rootReducer  from '../reducers'
import initialState from './initialState';
import apiMiddleware from '../middleware/apiMiddleware';
import formValidationMiddleware from '../middleware/formValidationMiddleware';

export default function configureStore() {
  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(
      formValidationMiddleware,
      apiMiddleware
    )
  )
} 
```

现在我们已经准备好编码我们的验证中间件了！

#### 执行验证

重要的事情先来。如果被分派的动作是`saveAstronaut`动作，那么我们*只需要*做这个验证工作。这是将发送 web 请求的动作，由我们的`apiMiddleware`提供。因此，我们将添加一个检查`"API"`动作类型的`if`语句。如果动作没有*或*具有那种类型，我们将返回`next(action)`，这样动作将进行到减速器。

[`client/src/middleware/formValidationMiddleware.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/middleware/formValidationMiddleware.js)T3】

```
const formValidationMiddleware = ({ dispatch, getState}) => next => action => {
  if (action.type !== "API") {
    return next(action)
  }
  // validations coming soon!
};

export default formValidationMiddleware; 
```

好了，继续我们的验证。我们将对每个需要验证的宇航员属性进行验证。通过将验证逻辑从组件中取出，我们负责决定是否将表单提交 API 请求也从组件中发出。我们允许组件调度`saveAstronaut`动作，不管是否存在任何错误。所以，我们*总是*想要验证*这个中间件中的所有*属性。

[`client/src/middleware/formValidationMiddleware.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/middleware/formValidationMiddleware.js)T3】

```
import { astronautValidationError } from '../actions/astronautActions';
import astronautValidationErrors    from '../utils/astronautValidationErrors';
import astronautIsValid             from '../utils/astronautIsValid';

const formValidationMiddleware = ({ dispatch, getState}) => next => action => {
  if (action.type != = "API") {
    return next(action)
  }
  const { astronaut } = action;
  let errors          = astronautValidationErrors(astronaut)
  if (!astronautIsValid(errors)) {
    dispatch(astronautValidationError(errors))
  } else {
    next(action)
  };
};

export default formValidationMiddleware; 
```

让我们分解一下，看看这里调用的一些辅助函数。

首先，我们从动作中抓住宇航员:

```
const { astronaut } = action; 
```

然后，我们在函数`astronautValidationErrors`的帮助下构建错误对象。

```
let errors = astronautValidationErrors(astronaut) 
```

我们的目标是生成一个看起来与 state 的`astronaut`键的`errors`子键完全一样的对象，其值正确地反映了错误的存在。我们希望生成这样一个对象，这样我们就可以将它发送给 reducer，后者将使用它来更新宇航员在应用程序状态中的错误。

例如，下面的 errors 对象将表明`name`属性有错误，而不是`email`或`terraform_planets`属性有错误。

```
{
  name: true,
  email: false,
  terraform_planets: false
} 
```

我们来看看 [`client/src/utils/astronautValidationErrors.js`](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/utils/astronautValidationErrors.js) :
中定义的`astronautValidationErrors`函数

```
import { attributeValidators } from './attributeValidators';

export default function astronautValidationErrors(astronaut) {
  Object.keys(attributeValidators).reduce((errors, validator) => {
    errors[validator] = !attributeValidators[validator](astronaut)
  }, {})
} 
```

这个函数依赖于我们从另一个`utils/`文件中导入的对象， [`attributeValidators`](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/utils/attributeValidators.js) :

```
export const attributeValidators = {
  name: nameValid,
  email: emailValid,
  terraform_planets: terraformPlanetValid
}

function nameValid(astronaut){
  return astronaut.name && astronaut.name.length > 0;
}

function emailValid(astronaut) {
  return astronaut.email && astronaut.email.split("@")[1] === "space.ex"
}

function terraformPlanetValid(astronaut) {
  const { terraform_experience, terraform_planets } = astronaut;
  if (terraform_experience) {
    return terraform_planets &&
      terraform_planets.length > 0 &&
      !terraform_planets.toLocaleLowerCase().includes("mars");
  } else {
    return true
  }
} 
```

这里我们有一个对象`attributeValidators`，它的键对应于每个宇航员的属性名，值指向我们定制的验证帮助函数。

我们在`astronautValidationErrors`函数中使用这个对象来:

*   通过属性的名称查找验证函数，调用该函数，
*   如果验证器返回 true(表示这个属性没有错误),则将我们正在构建的`errors`对象中的相同键设置为`false`,如果验证器返回 false(表示这个属性有错误),则设置为`true`。

```
errors[validator] = !attributeValidators[validator](astronaut) 
```

超级干净动感。

回到我们的中间件，我们已经生成了一个对象`errors`，它包含属性名称的键和`true`的值，以指示一个无效的属性，或者`false`指示没有这样的错误。

现在我们需要实现一些逻辑。如果`errors`对象包含*任何*真值(即，如果任何属性无效),我们*不应该*允许我们的动作继续到下一个中间件 API 中间件。我们应该调度一个新的动作，告诉 reducer 更新宇航员的错误状态。

```
// client/src/middleware/formValidationMiddleware.js
...
if (!astronautIsValid(errors)) {
  dispatch(astronautValidationError(errors))
} else {
  next(action)
} 
```

这里我们使用另一个助手函数，`astronautIsValid`。如果宇航员无效，我们将派遣`astronautValidtionError`行动。否则，我们将调用`next(action)`，让 Redux 继续将我们的动作传递给 API 中间件。

让我们来看看我们的助手函数，`astronautIsValid` :

```
// client/src/utils/astronautIsValid.js

export default function astronautIsValid(errors) {
  return !Object.values(errors).some(err => err)
} 
```

如果`errors`对象没有值为`true`的键(这表示一个无效属性),则返回 true 如果`errors`对象包含任何`true`值，则返回`false`。

回到我们的中间件，如果`errors`对象实际上包含`true`值，我们就用我们构建的`errors`对象的有效负载来调度`astronautValidtionError`动作。

#### 更新状态

`astronautValidtionError`动作看起来像这样:

```
// client/src/actions/astronautActions.js
...
export function astronautValidationError(errors) {
  return {
    type: "ASTRONAUT_VALIDATION_ERROR",
    errors
  }
} 
```

并由`astronautReducer`处理，它使用包含在`action.errors`中的对象来更新宇航员的状态，使其具有适当的错误:

```
// client/client/src/reducers/astronautReducer.js
...
case "ASTRONAUT_VALIDATION_ERROR":
  return {
    ...state,
    errors: {
    ...state.errors,
    ...action.errors
  }
} 
```

最后，如果给定的属性有错误，我们将更新每个组件以显示错误消息。

让我们以`AstronautEmail`组件为例来看看。

注意，容器组件`AstronautForm`现在作为一个道具传入了`this.props.astronaut.errors.email`。

```
// client/client/client/src/components/AstronautForm.js
...
render() {
  const { email, errors } = this.props.astronaut;
  ...
  <AstronautEmail
    email={email}
    emailError={errors.email}
    onAttributeUpdate={this.updateAstronautAttributes} />
  ...
} 
```

我们的`AstronautEmail`组件基于道具
中`emailError`的存在实现了一些显示逻辑

```
// client/client/src/components/form/AstronautEmail.js
...
render() {
  ...
  {emailError &&
    <div>please provide a valid SpaceEx email.</div>
  }
} 
```

在用户点击提交后，我们已经成功地验证了我们的表单，教会 Redux 管理应用程序状态中的错误，当宇航员无效时，*阻止了*web 请求被发送到 API，并在我们的组件中显示错误——所有这些都没有向我们的组件添加复杂的视图逻辑或状态管理！干得好，我们。

#### 状态改变时验证

现在我们已经看到了在提交表单时我们想要执行验证的场景，让我们讨论一下最后一个验证用例。当用户编辑表单时，应该进行一些验证--更新组件，以便在用户编辑完特定的表单域后立即显示某些错误。

我们的电子邮件和“你改造了哪些星球？”字段是这种期望行为的很好的例子。一旦用户的焦点离开这些表单域，我们应该显示或删除相应的错误。对于电子邮件，如果他们提供了非“@space.ex”的电子邮件，我们应该向他们显示一条错误消息。在地球化行星的情况下，如果(1)他们点击了“地球化体验”,但将此字段留空，或者(2)他们将“火星”包括在他们的行星列表中，我们应该向他们显示一个错误。

我们可以在下面看到这种行为:

那么，当我们从一个表单域中模糊出来，并在 Redux 的状态中更新宇航员的属性时，我们如何及时进入这个时间点呢？我们已经有一个动作被每个表单字段`updateAstronautAttributes`的`onBlur`调度。这个动作发送新的属性到 reducer，在那里宇航员的状态被更新。

让我们编写定制的中间件来拦截这个动作，验证宇航员的新属性，并为 reducer 的动作添加错误以包含任何状态变化。

我们将定义我们的中间件并将其添加到中间件堆栈中:

[`client/src/middleware/validateAttributeUpdateMiddleware.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/middleware/validateAttributeUpdateMiddleware.js)T3】

```
const validateAttributeUpdateMiddleware = ({ dispatch, getState}) => next => action => {
  // validations coming soon!
};

export default validateAttributeUpdateMiddleware; 
```

```
// client/src/store/configureStore.js

import {
  createStore,
  applyMiddleware } from 'redux'
import rootReducer  from '../reducers'
import initialState from './initialState';
import apiMiddleware from '../middleware/apiMiddleware';
import formValidationMiddleware from '../middleware/formValidationMiddleware';
import validateAttributeUpdateMiddleware from '../middleware/ValidateAttributeUpdateMiddleware';

export default function configureStore() {
  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(
      formValidationMiddleware,
      validateAttributeUpdateMiddleware,
      apiMiddleware
    )
  )
} 
```

现在我们已经准备好编写验证代码了！

[`client/src/middleware/validateAttributeUpdateMiddleware.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/middleware/validateAttributeUpdateMiddleware.js)T3】

```
import astronautAttribueIsValid from '../utils/astronautAttributeIsValid'

const ValidateAttributeUpdateMiddleware = ({ dispatch, getState}) => next => action => {
  if (action.type !== "UPDATE_ASTRONAUT_ATTRIBUTES") {
    return next(action)
  }
  const { newAttributes } = action;
  const { astronaut }     = getState();
  let updatedAstronaut    = {...astronaut, ...newAttributes}
  const attrName          = Object.keys(newAttributes)[0]
  action.errors = {
    [attrName]: !astronautAttribueIsValid(updatedAstronaut, attrName)
  }
  next(action)
};

export default ValidateAttributeUpdateMiddleware; 
```

让我们来分解一下:

首先，我们从动作中获取新属性的散列:

```
const { newAttributes } = action; 
```

然后，我们用新的属性:
构建当前状态的宇航员对象的副本

```
const { astronaut }     = getState();
let updatedAstronaut    = {...astronaut, ...newAttributes} 
```

接下来，我们需要获取当前正在更新的属性的名称，这样我们就知道要调用哪个验证帮助函数:

```
const attrName = Object.keys(newAttributes)[0] 
```

最后，我们用一个我们正在更新/验证的属性名称的键和一个`true/false`值动态填充`action.errors`。我们在另一个助手函数`astronautAttribueIsValid`的帮助下填充这个值。现在让我们来看看这个函数:

[`client/src/utils/astronautAttribueIsValid.js`:](https://github.com/SophieDeBenedetto/spacex-apply/blob/master/client/src/utils/astronautAttribueIsValid.js)T3】

```
import { attributeValidators } from './attributeValidators';

export default function astronautAttributeIsValid(astronaut, attribute) {
  if (attributeValidators[attribute]) {
    return attributeValidators[attribute](astronaut);
  } else {
    return true;
  }
} 
```

这个函数接受我们正在验证的宇航员对象的参数和要验证的属性的名称。

我们再次利用我们的`attributeValidators`对象和它存储的助手函数。我们通过验证函数的属性名来查找它，如果它存在，我们就用宇航员的参数来调用它。这将为有效属性返回`true`，为无效属性返回`false`。

如果我们试图在`attributeValidators`对象中查找一个验证函数返回`undefined`，那么这是一个我们*没有*验证器的属性。它不需要被验证，我们应该只返回`true`来表明属性是有效的(由于不需要验证，它*不可能*无效)。

所以，在宇航员的`newAttributes`看起来像这样的情况下:

```
{email: "sophie@gmail.com"} 
```

我们将`action.errors`设置为:

```
{
  email: true
} 
```

从而指示`email`属性无效。

#### 更新状态

一旦我们构建了错误对象并将其附加到`action`，我们就返回`next(action)`。这将把我们的动作发送到处于以下状态的减速器:

```
{
  type: "UPDATE_ASTRONAUT_ATTRIBUTES",
  newAttributes: {email: "sophie@email.com"},
  errors: {email: true}
} 
```

最后，我们将通过不仅更新宇航员的顶级属性，而且更新宇航员的错误来教会我们的`astronautReducer`正确处理这个动作。

```
// client/src/reducers/astronautReducer.js

...
case "UPDATE_ASTRONAUT_ATTRIBUTES":
  return {
    ...state,
    ...action.newAttributes,
    errors: {
      ...state.errors,
      ...action.errors
    }
  }
... 
```

这将导致组件重新渲染，将适当更新的`astronaut`从状态映射到`props`。我们的组件已经包含显示在`astronaut.errors`中发现的任何错误的逻辑，所以我们的应用程序应该只是工作！

### 结论

这里分享的代码仅仅代表了定制验证中间件的一小部分(人为的和简化的)用例。这里的要点是*不是*我们虚构的宇航员表单的特定验证函数，而是我们利用 Redux 处理这些验证的方式。我们避免了创建臃肿的容器组件，该组件负责验证并决定在什么情况下分派什么动作。相反，我们让 Redux 的集中式状态管理系统维护错误状态，并与不同动作的调度挂钩，以执行定制和复杂的验证。我们保持了组件的整洁，让 Redux 做它最擅长的事情。