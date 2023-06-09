# 使用 Dojo 的 Web 组件

> 原文：<https://dev.to/odoenet/web-components-with-dojo-kn9>

我们之前看到了如何用 [Dojo](https://dojo.io/) 创建一个[定制日期选择器](https://dev.to/odoenet/creating-a-datepicker-with-dojo-lbh)。如果用 Dojo 构建定制小部件还不够酷，Dojo 提供的一个使它与众不同的特性是能够将定制小部件导出到 [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)。

这非常有用，因为您可以在 Dojo 中创建一个封装的小部件，包括所有的业务逻辑，现在可以在任何需要的地方重用这个组件。

您可以在此查看关于如何将小部件导出到 web 组件[的文档。](https://dojo.io/docs/index.html#doc--dojo__cli-build-widget__v3_0_0--features--features)

你需要做的第一件事是`npm install @dojo/cli-build-widget`在我们的日期选择器项目中。完成后，我们只需要对应用程序中的小部件进行一些更新，以封装一些样式和其他小内容。

先来更新一下`src/widgets/styles/datepicker.m.css`。

```
@import url("https://fonts.googleapis.com/css?family=Roboto");
@import url("https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css");

.root {
    font-family: "Roboto", sans-serif;
  text-align: center;
  padding: 0.5em;
  color: #000;
}

input {
  font-size: 1.2em;
}

button {
  color: #fff;
  background: rgb(16, 184, 10);
  padding: 1em;
}

.hidden {
  display: none;
}

.calendarcontainer {
  background: #fff;
} 
```

现在我们已经包含了小部件的所有样式，包括小部件的 css 中所需的字体。

接下来，我们需要将`customElement`装饰器添加到我们的小部件中，以定义标签以及任何属性和事件信息。

```
// src/widgets/DatePicker.ts

import { v, w } from '@dojo/framework/widget-core/d';
import { WidgetBase } from '@dojo/framework/widget-core/WidgetBase';
import Calendar from '@dojo/widgets/calendar';
import EnhancedTextInput from '@dojo/widgets/enhanced-text-input';
// Used to define web component element tag name
import customElement from '@dojo/framework/widget-core/decorators/customElement';

import * as css from './styles/datePicker.m.css';

...

@customElement<DatePickerProperties>({
  tag: 'date-picker' // custom element can be defined as <date-picker></date-picker>
})
export class DatePicker extends WidgetBase<DatePickerProperties> {
  ...
}

export default DatePicker; 
```

现在我们需要做的最后一件事是用一个`"build-widget"`部分更新`.dojorc`。

```
{  "build-widget":  {  "elements":  ["src/widgets/DatePicker"]  }  } 
```

完成所有这些更改后，您现在应该能够运行`dojo build widget --mode dist`。

为了测试我们的 web 组件是否正常工作，我们可以创建一个使用它的新 HTML 文件。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link rel="stylesheet" href="./output/dist/date-picker/date-picker-1.0.0.css">
  <script src="./output/dist/date-picker/date-picker-1.0.0.js"></script>
  Date Picker Demo
</head>
<body>
  <!-- Custom Element -->
  <date-picker></date-picker>
</body>
</html> 
```

现在您应该看到一个带有日期选择器组件的[页面](https://odoe.github.io/dojo-datepicker-web-component/test.html)！

你可以在这里找到源代码[。](https://github.com/odoe/dojo-datepicker-web-component)

好了，我们刚刚导出了一个 Dojo 小部件，它是我们之前构建到一个可重用的 web 组件中的。用您自己的组件来尝试一下，并且确信您可以构建可以在任何地方使用的非常棒的小部件！

请务必[订阅 learn-dojo.com 的时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！