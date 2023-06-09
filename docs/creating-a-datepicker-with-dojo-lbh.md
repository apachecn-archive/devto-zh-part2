# 用 Dojo 创建日期选择器

> 原文：<https://dev.to/odoenet/creating-a-datepicker-with-dojo-lbh>

我最近谈到了关于@dojo/cli 的[入门。这一次，我想更深入一点，谈谈如何使用 Dojo 提供的一些](https://dev.to/odoenet/up-and-running-with-dojocli-242e)[小部件](https://github.com/dojo/widgets)来构建一个有用的[日期选择器小部件](https://github.com/odoe/dojo-datepicker-sample)。

[![](img/b7e5a80eec12c3c5fe88b25cb347adf7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DTGSeZJO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n08c1c5lnjj4ibj0uvlo.gif)

这个示例涵盖了 Dojo 的一些非常有趣的概念，包括小部件组合、样式、国际化的日期格式等等。

在我们开始之前，删除默认的`HelloWorld.ts`和带有`@dojo/cli`输出的小部件 css 文件。

## 创建微件

我们的第一步是安装小部件库。

```
npm install --save @dojo/widgets 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在`src/widgets`文件夹中创建一个`DatePicker.ts`。

```
// src/widgets/DatePicker.ts
import { v, w } from '@dojo/framework/widget-core/d';
import { WidgetBase } from '@dojo/framework/widget-core/WidgetBase';
import Calendar from '@dojo/widgets/calendar';

interface DatePickerProperties {
  selectedDate: Date;
}

interface DatePickerState {
  month?: number;
  year?: number;
  selectedDate?: Date;
}

export class DatePicker extends WidgetBase<DatePickerProperties> {
  state: DatePickerState = {
    month: 1,
    selectedDate: this.properties.selectedDate,
    year: 2018
  };

  protected render() {
    return v('div', [
      v(
        'section',
        {},
        [
          w(Calendar, {
            month: this.state.month,
            selectedDate: this.state.selectedDate,
            year: this.state.year,
            onMonthChange: (month: number) => {
              this.setState({ month: month });
            },
            onYearChange: (year: number) => {
              this.setState({ year: year });
            },
            onDateSelect: (date: Date) => {
              this.setState({ selectedDate: date });
            }
          })
        ]
      )
    ]);
  }

  // helper method to set the state
  protected setState(state: DatePickerState) {
    this.state = { ...this.state, ...state };
    this.invalidate();
  }
}

export default DatePicker; 
```

Enter fullscreen mode Exit fullscreen mode

在这个小部件中，我们将利用在`@dojo/widgets`中得到的[日历小部件](https://dojo.io/docs/index.html#doc--dojo__widgets__v3_0_0__src__calendar__README_md)。

这个小部件将有一些与选择日期相关的简单状态属性。

```
state = {
  month: 1,
  selectedDate: this.properties.selectedDate,
  year: 2018
}; 
```

Enter fullscreen mode Exit fullscreen mode

您可能还注意到了我添加的 helper 方法，它帮助我更新小部件的状态。

```
// helper method to set the state
protected setState(state: any) {
  this.state = { ...this.state, ...state };
  this.invalidate();
} 
```

Enter fullscreen mode Exit fullscreen mode

这将更新我的小部件的`state`对象，并调用一个`this.invalidate()`方法，该方法将根据新的状态变化更新我的小部件。在 Dojo 的未来版本中，这个`invalidate()`方法将被属性上的`@watch()`装饰器所取代，以简化更新。除此之外，我基本上遵循了文档中提供的日历[示例](https://dojo.io/docs/index.html#doc--dojo__widgets__v3_0_0__src__calendar__README_md)。

但是让我们把事情变得更有趣一点。我想要一个输入框，将显示我选择的日期。为此，我将使用[增强型文本输入](https://dojo.io/docs/index.html#doc--dojo__widgets__v3_0_0__src__enhanced-text-input__README_md)。

```
w(EnhancedTextInput, {
  addonAfter: [
    v(
      'button',
      {},
      [
        v('i', {
          classes: [
            'fa',
            'fa-chevron-down'
          ]
        })
      ]
    )
  ],
  label: 'Pick a date',
  value: this.state.selectedDate
}) 
```

Enter fullscreen mode Exit fullscreen mode

我想使用 EnhancedTextInput，因为它允许我使用`addonAfter`内容向它添加一个按钮。我发现这个很有用！你会注意到我正在用[字体牛逼的](https://fontawesome.com/v4.7.0/)来帮我解决这个问题。既然说到这了，那就来说说造型吧。

## CSS

创建一个`src/widgets/styles/datepicker.m.css`文件。

```
/* src/widgets/styles/datepicker.m.css */
.root {
    text-align: center;
    padding: 0.5em;
    color: #000;
}

.hidden {
  display: none;
}

.calendarcontainer {
  background: #fff;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我添加了一个`hidden`类。我们一会儿会用到它。但是首先，让我们修改一下`src/main.css`文件。

```
/* src/main.css */
@import url('https://fonts.googleapis.com/css?family=Roboto');
@import url('https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css');

body {
    font-family: 'Roboto', sans-serif;
    color: #fff;
    background: #1d1f20;
}
input {
  font-size: 1.2em;
}

button {
  color: #fff;
  background: rgb(16, 184, 10);
  padding: 1em;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我为我的输入、按钮和一些排版提供了一些样式。

有了应用程序所需的 css，我们就可以开始让小部件变得更具交互性了。

## 交互性

DatePicker 的部分功能是在单击按钮时打开和关闭日历。我们可以向状态对象添加一个`visible`属性。

```
state = {
  month: 1,
  selectedDate: this.properties.selectedDate,
  year: 2018,
  visible: false
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们修改按钮来更新`visible`状态。

```
v(
  'button',
  {
    onclick: () => {
      this.setState({ visible: !this.state.visible });
    }
  },
  [
    v('i', {
      classes: [
        'fa',
        this.state.visible ? 'fa-chevron-up' : 'fa-chevron-down'
      ]
    })
  ]
) 
```

Enter fullscreen mode Exit fullscreen mode

现在`onclick`将更新`visible`状态，这将决定按钮图标。我们还将更新日历的容器，以便它可以切换可见性。

```
v(
  'section',
  {
    classes: [this.state.visible ? '' : css.hidden, css.calendarcontainer]
  },
  [
    w(Calendar, {
      ...
    })
  ]
) 
```

Enter fullscreen mode Exit fullscreen mode

**牛逼！！**我们现在有了一个完全交互式的日期选择器。但是我们没有用选定的日期更新 EnhancedTextInput 的值。但是我们不能以任何正常的方式显示日期。我们希望我们的日期选择器支持各种地区。我们可以用 [`@dojo/framework/i18n`](https://dojo.io/docs/index.html#doc--dojo__framework__v3_0_0__src__i18n__README_md) 来做。

## 国际化

我不打算详细介绍如何使用`i18n`，它非常强大。但是我们将使用它来支持相应地格式化我们的日期。

首先，我们需要 [cldr-data](https://www.npmjs.com/package/cldr-data) 供我们的应用程序使用。

```
npm install --save cldr-data 
```

Enter fullscreen mode Exit fullscreen mode

接下来，更新`.dojorc`文件以使用它。

```
{  "build-app":  {  "locale":  "en",  "supportedLocales":  [  "es",  "fr",  "hi",  "ar",  "ja"  ],  "cldrPaths":  [  "cldr-data/main/{locale}/ca-gregorian",  "cldr-data/main/{locale}/dateFields",  "cldr-data/main/{locale}/numbers",  "cldr-data/main/{locale}/timeZoneNames",  "cldr-data/supplemental/likelySubtags",  "cldr-data/supplemental/numberingSystems",  "cldr-data/supplemental/ordinals",  "cldr-data/supplemental/plurals",  "cldr-data/supplemental/timeData",  "cldr-data/supplemental/weekData"  ]  },  "test-intern":  {},  "create-app":  {}  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以支持各种处理日期的语言环境。

```
// src/widgets/DatePicker.ts
import { formatDate } from '@dojo/framework/i18n/date';

...

w(EnhancedTextInput, {
  addonAfter: [
    ...
  ],
  label: 'Pick a date',
  value: formatDate(
    this.state.selectedDate || this.properties.selectedDate,
    { date: 'short' }
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用`formatDate()`方法相应地格式化 EnhancedTextInput 中的日期。我可以更进一步，提供不同地区的`Pick a date`文本，这并不难做到。你可以在这里阅读更多关于支持各种语言[的信息。](https://dojo.io/docs/index.html#doc--dojo__framework__v3_0_0__src__i18n__README_md--message-bundle-loading)

## 把这一切联系在一起

现在我们可以将 DatePicker 添加到我们的主应用程序中。

```
// src/main.ts
...

class App extends WidgetBase {
  protected render() {
    return v('div', [
      w(DatePicker, { selectedDate: new Date() })
    ]);
  }
}
... 
```

Enter fullscreen mode Exit fullscreen mode

瞧，瞧！您已经有了一个完整的、可使用的、具有风格化和国际化的日期选择器。我相信你可以比我做得更好，我从来没有声称自己是一个好的设计师，我只是知道足够的事情去做。

你可以在 [github](https://github.com/odoe/dojo-datepicker-sample) 上看到完整应用的源代码。

我还想指出的是，你可以在 [codesandbox](https://codesandbox.io) 中尝试一些 Dojo，尽管我在沙盒中使用`i18n`时遇到了一些问题，这就是为什么这里没有提供这个示例。

现在你可以继续前进，自己制作一些令人敬畏的部件了！

请务必[订阅 learn-dojo.com 的时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！