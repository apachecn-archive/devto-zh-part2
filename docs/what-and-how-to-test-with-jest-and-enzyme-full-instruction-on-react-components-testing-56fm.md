# React 中的组件测试:用 Jest 和 Enzyme 测试什么以及如何测试

> 原文：<https://dev.to/django_stars/what-and-how-to-test-with-jest-and-enzyme-full-instruction-on-react-components-testing-56fm>

测试 React 组件对于初学者和已经使用过测试的有经验的开发人员来说可能具有挑战性。将您自己的方法与我们在项目中使用的方法进行比较可能会很有趣。为了覆盖代码库，您必须知道哪些组件必须被测试，以及组件中的哪些代码应该被覆盖。

在阅读过程中，我将讨论以下主题:

*   根据项目结构定义组件测试的正确顺序
*   找出测试覆盖中要省略的内容(不要测试的内容)
*   确定快照测试的必要性
*   定义组件中要测试的内容以及测试顺序
*   提供详细的自定义代码示例

这篇文章要求读者已经有关于笑话和酶设置的知识。关于安装和配置的信息可以很容易地在网站或官方网站上找到。

假设以下情况:您需要用测试覆盖项目代码库，那么您应该从什么开始，在测试结束时您应该得到什么？100%的测试覆盖率？这是你应该追求的指标，但在大多数情况下你不会得到它。为什么？因为你不应该测试所有的代码。我们将找出为什么以及什么应该被排除在测试之外。更重要的是，100%的测试覆盖率并不总是确保组件被完全测试。同样，也不能保证它会通知你是否有什么变化。不要追求百分比，避免编写虚假的测试，尽量不要丢失主要的组件细节。

## 根据项目结构定义组件测试的正确顺序

让我们在项目结构的下一部分讨论这个问题:

[![defining-queue-for-project-structure](img/f6a9d7003f8cceeb89e8e7ffab8711b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ng8GE8sK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/Img-1--2-.png)

我选择了`shared`目录，因为它是最重要的；它由项目的几个不同页面中使用的组件组成。它们是可重复使用的，通常很小，也不复杂。如果一个或另一个组件出现故障，将会导致其他地方出现故障。这就是为什么我们应该确信它们是否写得正确。该目录的结构分为几个文件夹，每个文件夹包含组件。

[![project-structure](img/43102938806fc4b1dfe53c0ebd67ac65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zdr91APb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/Img-2--3-.png)

如何定义`shared`目录中组件测试的正确顺序:

*   永远遵循从简单到复杂的规则。分析每个目录并定义哪些组件是`independent`——也就是说，它们的渲染不依赖于其他组件；它们是独立完成的，可以作为一个单元单独使用。从上面的结构来看，是在`forms`文件夹下的`inputs`目录。它包含 redux-forms 的输入组件，如 TextInput、SelectInput、CheckboxInput、DateInput 等。
*   接下来，我需要定义`inputs`组件中经常使用的辅助组件，但应该与它们分开测试。它是`utils`目录。这个文件夹中的组件并不复杂，但是非常重要。它们通常是可重用的，有助于重复操作。
*   下一步是定义哪些组件也可以独立使用。如果有的话，带他们去化验。从我们的结构来看，它是`widgets`，具有简单功能的小组件。它们将是测试覆盖队列中的第三个项目。
*   此外，分析其余的目录并定义更复杂的组件，这些组件可以单独使用，也可以与其他组件结合使用。在我们的例子中是`modals`目录；这些组件将在下面详细解释。
*   最复杂的测试组件留在了最后。它们是`hoc`目录和来自`forms`文件夹的`fields`。你怎么定义应该先测试哪一个？我从目录中取出已经在测试组件中使用的组件。因此，`hoc`目录中的组件出现在`widgets`组件中；这就是为什么我已经知道这个目录和它的组件在哪里被使用以及用于什么目的。
*   最后一个是`fields`文件夹；它包含与 redux-forms 连接的组件。

最终的组件顺序(基于我们的示例)如下所示:

[![components-order](img/990cd46771a8011b2ce3914b64bde9d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0rtJd5ZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/Img-3--1-.png)

按照这个顺序，你一步一步地增加被测试组件的复杂性；因此，当操作更复杂的组件时，您已经知道最小的组件是如何工作的。例如，不要拿‘数组’字段来进行测试，如果你不确定如何测试‘文本’字段；如果你没有测试过“表单”字段本身，就不要使用用 redux-form 修饰的组件。选择要一致，不要拿第一个想到的组件，打开逻辑。当然，你的项目的结构可以不同；它可以有其他目录名，也可以有附加的组件、动作和缩减器，但是定义组件测试顺序的逻辑是相同的。

**让我们定义测试覆盖中应该省略的内容:**

1.  ***第三方库*** 。不要测试来自另一个库的功能；你不用对代码负责。如果你需要它来测试你的代码，跳过它或者模仿实现。
2.  ***常数*** 。名字本身就说明了一切。它们是不可改变的；这是一组静态代码，不打算改变。
3.  *(如果你在组件中使用它们)。为了测试内联样式，您需要在测试中复制带有样式的对象；如果样式对象改变了，你也必须在测试中改变它。不要在测试中复制组件的代码；你永远不会记得在测试中改变它。此外，你的同事永远也猜不到重复。在大多数情况下，内联样式不会改变组件的行为；因此，它们不应该被测试。在你的风格动态变化的情况下，可以有一个例外。*
**   ***与被测元件无关的事物*** 。跳过测试组件中导入的测试组件的覆盖；小心它是否被另一个包裹着。不要测试 wrapper，单独分析测试即可。*

 *那么，实际上如何编写测试呢？我结合了两种测试方法:

*   快照测试
*   组件逻辑测试

如果你想确定用户界面没有改变，快照测试是一个有用的测试工具。当第一次面对这个测试工具时，出现了关于组织和管理快照的问题。工作原理很简单，可惜哪里都没有完整描述过；在官网 jestjs.io 上，对快照测试工作的描述很差。

## 如何用快照进行测试

**第一步。**为组件编写测试，在 expect 块中，使用创建`Snapshot`本身的`.toMatchSnapshot()`方法。

```
it('render correctly text component', () => {
    const TextInputComponent = renderer.create(<TextInput />).toJSON();
    expect(TextInputComponent).toMatchSnapshot();
}); 
```

**第二步。**当您第一次在一个级别上运行测试时，随着测试的进行，会有一个名为`__snapshots__`的创建目录，里面有自动生成的文件，扩展名为`.snap`。

快照看起来像:

```
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`Render TextInput correctly component 1`] = `
<input
  className="input-custom"
  disabled={undefined}
  id={undefined}
  name={undefined}
  onBlur={undefined}
  onChange={[Function]}
  pattern={undefined}
  placeholder={undefined}
  readOnly={false}
  required={undefined}
  type="text"
  value={undefined}
/>
`; 
```

**第三步。**将快照推送到存储库中，并与测试一起存储。

如果组件已经改变，您只需要用`—updateSnapshot`标志或使用快照形式`u`标志更新快照。

### 快照被创建；它是如何工作的？

让我们考虑两种情况:

#### 1。组件已更改

*   运行测试
*   创建新的快照，它与存储在`__snapshots__`目录中的自动生成的快照进行比较
*   测试失败，因为快照不同

[![test-failed](img/0eb4bc89a46ca8ff9eaa9741d4f84ce1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ORGjVET--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/fail.gif)

#### 2。组件没有改变

*   运行测试
*   创建新的快照，它与存储在`__snapshots__`目录中的自动生成的快照进行比较
*   测试通过，因为快照相同

[![test-succes](img/fbadbca01ef2fcfd71600de4429074cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9BUOzmFA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/success.gif)

当我测试一个没有逻辑的小组件时，一切都很好，只是 UI 渲染，但实践表明，真正的项目上没有这样的组件。如果有，也是少量的。

是否有足够的快照进行完整的组件测试？

## 组件测试的主要说明

**1。一个组件应该只有一个快照。如果一个快照失败了，其他的很可能也会失败，所以不要创建和存储一堆不必要的快照，这会阻塞空间，让开发人员困惑，他们会在你之后阅读你的测试。当然，当您需要测试一个组件在两种状态下的行为时，也有例外；例如，打开弹出窗口之前和打开之后组件的状态。然而，即使是这样的变体也总是可以被这个替换:第一个测试存储组件的默认状态，在快照中没有弹出窗口，第二个测试模拟事件并检查特定类的存在。通过这种方式，您可以轻松地绕过创建多个快照。**

**2。测试道具:**作为一个规则，我把道具的测试分为两个测试:

*   首先，检查默认属性值的渲染；当组件被渲染时，如果这个道具有`defaultProps`，我希望它的值等于`defaultProps`。
    *   其次，查看道具的自定义值；我设置了自己的值，并期望在组件呈现后收到它。

**3。测试数据类型:**为了测试道具中出现了什么类型的数据，或者在某些动作之后获得了什么类型的数据，我使用了特殊的库 jest-extended(附加 jest matchers)，它有一个 Jest 中没有的扩展匹配集。有了这个库，测试数据类型变得更加容易和愉快。测试 proptypes 是一个矛盾的问题。一些开发人员可以反对 proptypes 测试，因为它是第三方包，不应该测试，但我坚持测试组件的 proptypes，因为我不测试包功能本身；我只是确保属性类型是正确的。数据类型是非常重要的编程部分，不应该被忽略。

**4。事件测试:**在创建快照并用测试覆盖 props 之后，您可以确保组件的正确呈现，但是如果组件中有事件，这还不足以完全覆盖。您可以用几种方式检查事件；最广泛使用的是:

*   模拟事件= >模拟它= >调用了预期事件
*   模拟事件= >用参数模拟事件= >用传递的参数调用预期事件
*   传递必要的属性= >渲染组件= >模拟事件= >在被调用的事件上预期某种行为

**5。测试条件:**很多时候你可以有特定类输出的条件，渲染某段代码，调用所需道具等等。不要忘记这一点，因为使用默认值，只有一个分支将通过测试，而第二个分支将保持未测试。在具有计算和大量条件的复杂组件中，您可能会错过一些分支。为了确保代码的所有部分都被测试覆盖，使用测试覆盖工具，直观地检查哪些分支被覆盖，哪些没有被覆盖。

**6。状态测试:**为了检查状态，在大多数情况下，有必要编写两个测试:

*   第一个检查当前状态。
*   第二个在调用事件后检查状态。Render component = >在测试中直接调用函数= >检查状态是如何改变的。要调用组件的函数，您需要获得组件的一个实例，然后才调用它的方法(示例见[下一个测试](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/modals/__tests__/ModalTrigger.test.js#L30))。

在您完成这个说明列表后，您的组件将被覆盖 90%到 100%。我将 10%留给文章中没有描述、但代码中可能出现的特殊情况。

## 测试的例子

让我们转到例子，一步一步地在上述结构下测试组件。

#### 1。根据表单/输入测试组件。

从表单/输入目录中取出一个组件；假设它是 datepicker 字段的组件 DateInput.js。

**被测组件的代码清单: [DateInput.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/forms/inputs/DateInput.js)

看起来像:**

[![date-picker-test](img/0a2642090975bcdbad1812d73eba63af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hOlYKA4y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/Datepicker.gif)

DateInput 组件使用库 react-datepicker，带有两个实用程序:valueToDate(将值转换为日期)和 dateToValue，反之亦然，用于操作日期的 moment package 和用于检查 react 属性的 PropTypes。

根据组件代码，我们可以看到默认道具的列表，在这些默认道具的帮助下，组件应该被渲染:

```
const defaultProps = {
    inputClassName: 'input-custom',
    monthsShown: 1,
    dateFormat: 'DD.MM.YYYY',
    showMonthYearsDropdowns: false,
    minDate: moment()
}; 
```

所有的道具都适合创建快照，除了一个`minDate: moment()`，moment()将在我们每次运行测试时给出当前日期，快照将失败，因为它存储了过期的日期。解决方法是模拟这个值:

```
const defaultProps = {
    minDate: moment(0)
} 
```

minDate prop 我需要在每个渲染组件；为了避免道具重复，我创建了 HOC，它接收 defaultProps 并返回 pretty 组件:

```
import TestDateInput from '../DateInput';
const DateInput = (props) =>
    <TestDateInput
        {...defaultProps}
        {...props}
    />; 
```

不要忘记`moment-timezone`，尤其是如果您的测试将由来自不同时区的另一个国家的开发人员运行。他们将收到嘲笑的价值，但随着时区的变化。解决方法是设置默认时区:

```
const moment = require.requireActual('moment-timezone').tz.setDefault('America/Los_Angeles') 
```

现在，日期输入组件已准备好进行测试:

**1。先创建快照:**

```
it('render correctly date component', () => {
    const DateInputComponent = renderer.create(<DateInput />).toJSON();
    expect(DateInputComponent).toMatchSnapshot();
}); 
```

**2。测试道具:**

翻遍道具，找到重要的；要测试的第一个属性是 showMonthYearsDropdowns，如果它设置为 true，则显示月份和年份的下拉列表:

```
it('check month and years dropdowns displayed', () => {
    const props = {
            showMonthYearsDropdowns: true
        },
        DateInputComponent = mount(<DateInput {...props} />).find('.datepicker');
    expect(DateInputComponent.hasClass('react-datepicker-hide-month')).toEqual(true);
}); 
```

测试空属性值；需要进行此检查，以确保组件在没有定义值的情况下呈现:

```
it('render date input correctly with null value', () => {
    const props = {
            value: null
        },
        DateInputComponent = mount(<DateInput {...props} />);
    expect((DateInputComponent).prop('value')).toEqual(null);
}); 
```

**3。值的测试属性类型，日期应为字符串:**

```
it('check the type of value', () => {
    const props = {
            value: '10.03.2018'
        },
        DateInputComponent = mount(<DateInput {...props} />);
    expect(DateInputComponent.prop('value')).toBeString();
}); 
```

**4。测试事件:**

**4.1。**检查 onChange 事件，对于那个模拟 onChange 回调= >呈现日期输入组件= >，然后用新的目标值模拟 Change 事件= >，最后检查 onChange 事件是否已经用新的值调用。

```
it('check the onChange callback', () => {
    const onChange = jest.fn(),
        props = {
            value: '20.01.2018',
            onChange
        },
        DateInputComponent = mount(<DateInput {...props} />).find('input');
    DateInputComponent.simulate('change', { target: {value: moment('2018-01-22')} });
    expect(onChange).toHaveBeenCalledWith('22.01.2018');
}); 
```

**4.2。**确保 datepicker 弹出窗口在点击日期输入后打开，对于该查找日期输入= >模拟点击事件= >并期望带有类`.react-datepicker`的弹出窗口出现。

```
it('check DatePicker popup open', () => {
    const DateComponent = mount(<DateInput />),
        dateInput = DateComponent.find("input[type='text']");
    dateInput.simulate('click');
    expect(DateComponent.find('.react-datepicker')).toHaveLength(1);
}); 
```

**完整测试列表:** [DateInput.test.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/forms/inputs/__tests__/DateInput.test.js)

#### 2。效用测试:

**测试实用程序代码清单:** [valueToDate.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/utils/valueToDate.js)

此实用程序的目的是用自定义格式转换值。
首先，让我们分析给定的效用并定义测试的主要情况:

**1。**根据这个实用程序的用途，它转换值，所以我们需要检查这个值:

*   如果没有定义值:我们需要确保实用程序不会返回异常(错误)。
*   在定义值的情况下:我们需要检查实用程序返回时刻日期。

**2。**返回值应该属于矩类；这就是为什么它应该是时刻的实例。

**3。**第二个参数是 dateFormat 测试前将其设置为常量。这就是为什么它会在每个测试中传递，并根据日期格式返回值。我们应该单独测试日期格式吗？我想没有。这个参数是可选的；如果我们不设置 dateFormat，这个实用程序不会中断，它只是以默认格式返回日期；这是一个临时的工作，我们不应该测试第三方库。
正如我之前提到的，我们不应该忘记时间时区；这是非常重要的一点，尤其是对于来自不同时区的开发者来说。

**咱们来码:**

**1。为第一个案例编写测试；当我们没有价值的时候，它就是空的。**

```
 const format = 'DD.MM.YYYY';

it('render valueToDate utility with empty value', () => {
    const value = valueToDate('', format);
    expect(value).toEqual(null);
}); 
```

**2。检查值是否已定义。**

```
const date = '21.11.2015',
      format = ‘DD.MM.YYYY’;

it('render valueToDate utility with defined value', () => {
    const value = valueToDate(date, format);
    expect(value).toEqual(moment(date, format));
}); 
```

**3。检查值是否属于矩类。**

```
const date = '21.11.2015',
    format = 'DD.MM.YYYY';

it('check value is instanceof moment', () => {
    const value = valueToDate(date, format);
    expect(value instanceof moment).toBeTruthy();
}); 
```

**完整测试列表:** [valueToDate.test.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/utils/__tests__/valueToDate.test.js)

#### 3。小部件测试

对于小部件测试，我选择了 spinner 组件。
**被测控件代码清单:** [Spinner.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/widgets/Spinner.js)

***长相:***

[![alt](img/7457818040675b19cc2b7430a9f8f33b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Mo4jJ1h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/spinner.gif)

解释中不需要 Spinner，因为几乎所有的 web 资源都有这个组件。
所以去写测试吧:

**1。第一步-创建快照:**

```
it('render correctly Spinner component', () => {
   const SpinnerComponent = mount(<Spinner />);
   expect(SpinnerComponent).toMatchSnapshot();
}); 
```

**2。测试道具:**

**2.1** 默认道具标题，检查是否渲染正确。

```
it('check prop title by default', () => {
 const SpinnerComponent = mount(<Spinner />);
    expect(SpinnerComponent.find('p').text()).toEqual('Please wait');
}); 
```

**2.2** 查看自定义道具标题；我需要检查它是否返回正确定义的属性。看一下代码，用 rawMarkup util 包装的标题，以及借助 dangerouslySetInnerHTML 属性的输出。

原始标记实用程序的代码列表:

```
 export default function rawMarkup(template) {
    return {__html: template};
} 
```

我们需要在 spinner 组件中包含 rawMarkup 的测试吗？不，它是一个独立的工具，应该与旋转器分开测试。我们不关心它是如何工作的；我们只需要知道标题本身返回正确的结果。

澄清:使用 dangerouslySetInnerHTML 属性的原因如下。我们的网站是多语言的，由翻译营销团队负责。他们可以简单地用单词组合来翻译，甚至用 html 标签来装饰，比如`<strong>`、`<i>`、`<s>`，甚至用列表`<ol>`、`<ul>`来分割文本；我们不确定他们是如何翻译和修饰文本的。我们只需要正确地渲染这些东西。

我在一个测试中结合了两个主要的测试用例:

*   返回正确的自定义道具标题
*   使用 html 标签正确呈现正确标题

```
it('check prop title with html tags', () => {
    const props = {
            title: '<b>Please wait</b>'
        },
        SpinnerComponent = mount(<Spinner {...props} />);
    expect(SpinnerComponent.find('p').text()).toEqual('Please wait');
}); 
```

取下一个道具字幕；它是可选的，这就是为什么它没有默认道具，所以跳过默认道具的步骤，测试自定义道具:

*   检查字幕中文本是否正确呈现:

```
const props = {
        subTitle: 'left 1 minute'
    },
    SpinnerComponent = mount(<Spinner {...props} />); 
it('render correct text', () => {
    expect(SpinnerComponent.find('p').at(1).text()).toEqual(props.subTitle);
}); 
```

我们知道字幕是可选的；这就是为什么我们需要根据切片标记检查它是否是用默认道具渲染的。只需检查标签的数量`<p>` :

```
it('check subTitle is not rendered', () => {
  const SpinnerComponent = mount(<Spinner />);
    expect(SpinnerComponent.find('p').length).toEqual(1);
}); 
```

**3。测试道具类型:**

*   对于预期为字符串的标题属性:

```
 it('check prop type for title is string', () => {
    const props = {
            title: 'Wait'
        },
        SpinnerComponent = mount(<Spinner {...props} />);
    expect(SpinnerComponent.find('p').text()).toBeString();
}); 
```

*   对于字幕属性也应该是字符串:

```
const props = {
        subTitle: 'left 1 minute'
    },
    SpinnerComponent = mount(<Spinner {...props} />); 
it('type for subTitle is string', () => {
    expect(SpinnerComponent.find('p').at(1).text()).toBeString();
}); 
```

**完整测试列表:** [Spinner.test.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/widgets/__tests__/Spinner.test.js)

#### 4。模态测试(ModalWrapper.js 和 ModalTrigger.js)

***长相:***
[![modal-testing](img/bcd61d99c9fc9074f9e9393adf30cde1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V24YBCiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://djangostars.com/blog/conteimg/2018/09/modal.gif)

如何测试情态动词:
首先，我想解释一下在我们的项目中情态动词是如何组织的。我们有两个组件: **ModalWrapper.js** 和 **ModalTrigger.js** 。

**ModalWrapper** 负责弹出布局。它包含模式容器、按钮“关闭”、模式标题和主体。

**模态触发器**负责模态处理。它包括 ModalWrapper 布局，并包含 modal 布局控件的事件(打开、关闭操作)。

我分别概述了每个组件:

**1。被测组件代码列表:** [ModalWrapper.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/modals/ModalWrapper.js)

**咱们来码:**

**1.1** ModalWrapper 接收组件并在内部呈现。首先检查 ModalWrapper 没有组件不会失败。用默认道具创建快照:

```
 it('without component', () => {
    const ModalWrapperComponent = shallow(<ModalWrapper />);
    expect(ModalWrapperComponent).toMatchSnapshot();
}); 
```

**1.2** 下一步是通过道具组件渲染来模拟它的实际情况:

```
it('with component', () => {
   const props = {
           component: () => {}
        },
        ModalWrapperComponent = shallow(<ModalWrapper {...props} />);
    expect(ModalWrapperComponent).toMatchSnapshot();
}); 
```

**1.3** 测试道具:

接收自定义类名属性:

```
it('render correct class name', () => {
    const props = {
            modalClassName: 'custom-class-name'
        },
        ModalWrapperComponent = shallow(<ModalWrapper {...props} />).find('Modal');
        expect(ModalWrapperComponent.hasClass('custom-class-name')).toEqual(true);
}); 
```

接收自定义标题道具:

```
it('render correct title', () => {
    const props = {
           title: 'Modal Title'
       },
       ModalWrapperComponent = shallow(<ModalWrapper {...props} />).find('ModalTitle');
    expect(ModalWrapperComponent.props().children).toEqual('Modal Title');
}); 
```

接收正确的显示道具:

```
 it('check prop value', () => {
        const props = {
               show: true
           },
           ModalWrapperComponent = shallow(<ModalWrapper {...props} />).find('Modal');
        expect(ModalWrapperComponent.props().show).toEqual(true);
    }); 
```

**1.4** 测试支撑类型:

*   为了表演道具

```
 it('check prop type', () => {
    const props = {
           show: true
        },
        ModalWrapperComponent = shallow(<ModalWrapper {...props} />).find('Modal');
    expect(ModalWrapperComponent.props().show).toBeBoolean();
}); 
```

*   对于舷侧支柱

```
it('render correct onHide prop type', () => {
    const props = {
            onHide: () => {}
        },
        ModalWrapperComponent = shallow(<ModalWrapper {...props} />).find('Modal');
    expect(ModalWrapperComponent.props().onHide).toBeFunction();
}); 
```

*   对于部件本身

```
it(‘render correct component prop type’, () => {
   const props = {
           component: () => {}
       },
       ModalWrapperComponent = mount(<ModalWrapper {...props} />);
   expect(ModalWrapperComponent.props().component).toBeFunction();
}); 
```

**完整测试列表:** [ModalWrapper.test.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/modals/__tests__/ModalWrapper.test.js)

**2。被测组件代码列表:** [ModalTrigger.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/modals/ModalTrigger.js)

模态包装覆盖了测试；第二部分是模态触发器组件。
组件概述:基于状态`toggled`，表示 ModalWrapper 的可见性。如果`toggled: false`，弹出窗口隐藏，否则可见。函数 **open()** 在子元素上打开弹出窗口；click 事件和函数 **close()** 隐藏在 ModalWrapper 中呈现的按钮上的弹出窗口。

**2.1** 快照创建:

```
it('render ModalTrigger component correctly', () => {
    const ModalTriggerComponent = shallow(<ModalTrigger><div /></ModalTrigger>);
    expect(ModalTriggerComponent).toMatchSnapshot();
}); 
```

应该用组件道具渲染测试 ModalTrigger 吗？没有；因为`component`将在 ModalWrapper 组件内部呈现，所以它不依赖于被测试的组件。它已经包含在 ModalWrapper 测试中了。

**2.2** 测试道具。我们有一个道具，我们想确定我们只有一个孩子。

```
it('ensure to have only one child (control element)', () => {
    expect(ModalTriggerComponent.findWhere(node => node.key() === 'modal-control').length).toEqual(1);
}); 
```

**2.3** 测试道具类型。儿童道具应该是物体，在下一个测试中检查这个:

```
const ModalTriggerComponent = mount(<ModalTrigger><div /></ModalTrigger>); 
it('check children prop type', () => {
      expect(ModalTriggerComponent.props().children).toBeObject();
}); 
```

2.4 modal trigger 组件的重要部分是检查状态。
我们有两种状态:
弹出打开。要知道模态是打开的，我们需要检查它的状态。为此，从组件的实例调用 open 函数，并期望状态中的`toggled`应该为真。

```
 it('check the modal is opened', () => {
    const event = {
        preventDefault: () => {},
        stopPropagation: () => {}
    };
    ModalTriggerComponent.instance().open(event);
    expect(ModalTriggerComponent.state().toggled).toBeTruthy();
}); 
```

弹出框是关闭的，被检测者反之亦然，`toggled`处状态应为假。

```
 it('check the modal is closed', () => {
   ModalTriggerComponent.instance().close();
   expect(ModalTriggerComponent.state().toggled).toBeFalsy();
}); 
```

**完整测试列表:** [ModalTrigger.test.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/modals/__tests__/ModalTrigger.test.js)

现在模态已经完全测试过了。测试相互依赖的组件的一个建议是:首先检查组件并编写测试计划，定义每个组件中需要测试的内容，检查每个组件的测试用例，并确保不要在两个组件中重复相同的测试用例。仔细分析测试覆盖的可能的和最佳的变体。

#### 5。特设测试(高阶组件)

最后两个部分(HOC 和表单的字段测试)是相互关联的。我想与你分享如何测试现场布局与它的特设。
解释什么是基本字段布局，为什么我们需要这个组件，以及我们在哪里使用它:

*   js 是表单输入组件的包装器，如 TextInput、CheckboxInput、DateInput、SelectInput 等。它们的名字以`-Input`结尾，因为我们使用 redux-form 包，这些组件是 redux-form 逻辑的输入组件。
*   我们需要为表单域组件创建布局的 BaseFieldLayout，即呈现标签、工具提示、前缀(货币、平方米缩写等。)，图标，错误…
*   我们在 BaseFieldHOC.js 中使用它来包装字段布局中的 inputComponent，并借助于`<Field/>`组件将其与 redux-form 连接起来。

**被测组件代码列表:** [BaseFieldHOC.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/hoc/BaseFieldHOC.js)

它是一个接收表单输入组件并返回组件的特设组件，与 redux-form 连接。

分析特设:

*   这个组件只接收一个道具`component`。首先，我需要创建这个组件，并将其包装在 BaseFieldHOC 中。
*   接下来，我需要用 redux-form 来修饰包装后的 HOC，以便用 redux-form 连接字段。
*   在 React Redux `<Provider>`组件中呈现该字段，以使测试组件可以使用该存储。要模拟商店，只需:

```
const store = createStore(() => ({})); 
```

现在，在每次测试之前，我需要做下一步:

```
 let BaseFieldHOCComponent;

beforeEach(() => {
    const TextInput = () => { return 'text input'; },
        BaseFieldHOCWrapper = BaseFieldHOC(TextInput),
        TextField = reduxForm({ form: 'testForm' })(BaseFieldHOCWrapper);
    BaseFieldHOCComponent = renderer.create(
        <Provider store={store}>
            <TextField name="text-input" />
        </Provider>
    ).toJSON();
}); 
```

之后，组件就可以进行测试了:

1.创建快照:

```
 it('render correctly component', () => {
    expect(BaseFieldHOCComponent).toMatchSnapshot();
}); 
```

2.确保渲染后输入组件被包装在 BaseFieldLayout 中:

```
 it('check input component is wrapped in BaseFieldLayout', () => {
    expect(BaseFieldHOCComponent.props.className).toEqual('form-group');
}); 
```

仅此而已，特设已涵盖。与 redux 表单组件相关的测试中最复杂的部分是做好现场准备(用 redux 表单装饰并设置存储)；剩下的就简单了，只要按照说明做就行了，别无其他。
**全面测试列表:** [BaseFieldHOC.test.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/hoc/__tests__/BaseFieldHOC.test.js)

#### 6。表单/字段测试

字段 HOC 已经覆盖了测试，我们可以移动到 BaseFieldLayout 组件。

**被测组件代码列表:** [BaseFieldLayout.js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/forms/fields/BaseFieldLayout.js)

我们来编码 BaseFieldLayout.js 根据上面的说明编写测试:

**1。首先，创建快照。**

如果没有 defaultProps，将不会呈现此组件:

*   输入组件
*   redux-form 提供的道具:输入和元对象。属性名称为的输入和属性错误的元已被触及:

```
const defaultProps = {
   meta: {
        touched: null,
        error: null
    },
    input: {
        name: 'field-name'
    },
    inputComponent: () => { return 'test case'; }
} 
```

要在每个测试的包装器中使用 defaultProps，请执行以下操作:

```
import TestBaseFieldLayout from '../BaseFieldLayout';

const BaseFieldLayout = (props) => <TestBaseFieldLayout {...defaultProps} {...props} />; 
```

现在我们准备创建快照:

```
it('render correctly BaseFieldLayout component', () => {
    const BaseFieldLayoutComponent = renderer.create(<BaseFieldLayout />).toJSON();
    expect(BaseFieldLayoutComponent).toMatchSnapshot();
}); 
```

**2。测试道具:**

这个组件有很多道具。我将展示几个例子；其余的用类比法测试。

*   确保`icon`道具被正确渲染

```
it('render correctly icon prop', () => {
    const props = {
            icon: <span className="icon-exclamation" />
        },
        BaseFieldLayoutComponent = mount(<BaseFieldLayout {...props} />);
        expect(BaseFieldLayoutComponent.find('span').hasClass('icon-exclamation')).toBeTruthy();
}); 
```

*   确保工具提示内容呈现在标签旁边

```
 const props = {
        labelTooltipContent: 'tooltip for label'
    },
    BaseFieldLayoutComponent = mount(<BaseFieldLayout {...props} />); 
it('check prop is rendered', () => {
   expect(BaseFieldLayoutComponent.find('span').hasClass('tooltip-icon')).toBeTruthy();
}); 
```

*   测试`fieldLink`道具
    *   确保默认情况下 fieldLink 为空

```
 it('check prop is null by default', () => {
    const BaseFieldLayoutComponent = shallow(<BaseFieldLayout />);
    expect(BaseFieldLayoutComponent.props().fieldLink).toBe(null);
}); 
```

*   确保 fieldLink 使用自定义值正确呈现

**3。测试错误:**

```
it('check if field has error', () => {
    const props = {
            meta: {
                touched: true,
                error: 'This field is required'
            }
        },
        BaseFieldLayoutComponent = mount(<BaseFieldLayout {...props} />);
    expect(BaseFieldLayoutComponent.find('.error')).toHaveLength(1);
}); 
```

**完整测试列表:**[basefieldlayout . test . js](https://github.com/ned-alyona/testing-jest-enzyme/blob/master/shared/forms/fields/__tests__/BaseFieldLayout.test.js)

## 底线

现在，您已经对如何基于项目结构执行组件的全覆盖测试有了全面的指导。根据我自己的经验，我试图解释什么是测试所必需的，以什么样的顺序，以及在测试覆盖中你可以省略什么。此外，我演示了几个测试组件的例子，并指出代码库覆盖的顺序。我希望你会发现这篇文章是有用的，并分享你的回应。感谢您的阅读。

这篇文章是由 Alyona Pysarenko——前端开发人员撰写的。这篇关于 [react 组件测试](https://djangostars.com/blog/what-and-how-to-test-with-enzyme-and-jest-full-instruction-on-react-component-testing/)的文章最初发表在 Django Stars 博客上。您还可以访问我们的内容平台[产品部落](https://producttribe.com/)，它是由专业人士为那些参与产品开发和成长过程的人创建的。

我们随时欢迎您提出问题，分享您想阅读的话题！*