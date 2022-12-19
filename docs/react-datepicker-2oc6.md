# 如何用 React 构建自定义日期选择器

> 原文：<https://dev.to/bnevilleoneill/react-datepicker-2oc6>

[![](../Images/7ee0cf0518286686a698038b89c735e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9eSfznFM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AU944VbSXNnjEB0x-zhTslg.jpeg) 

<figcaption>原图由 [rawpixel](https://unsplash.com/photos/ZMMXSRMSoI8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/calendar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

在 web 上看到包含一个或多个日期字段的表单是很常见的。无论是需要一个人的出生日期还是航班时刻表日期，您总是希望确保用户提供有效的日期。

在 HTML5 中，引入了新的日期输入类型，以确保更好地在表单中获取有效的日期值。日期输入类型的默认行为是向用户显示一个日期选择器。但是，这种日期选择器的外观在不同的浏览器之间并不一致。

你可以在这里找到更多关于日期输入类型和浏览器支持的信息。

[![](../Images/2bc5c2cd4cb0da7319feeebb0913c124.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Av6Qv025--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxMzl9zhZnW-ATJ3d.jpg)

在本教程中，您将学习如何使用 React 和本地 JavaScript 日期对象从头构建一个自定义日期选择器。下面是一个简短的演示，展示了日期选择器的外观。

[![](../Images/bb32e6b034af2cc447740ee57bc88cdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lp09YQB_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/0%2A56OXuBzi5_p9egND.gif)

还可以在**代码沙箱**上获得现场演示。

[https://codesandbox.io/embed/o952xlqq9y](https://codesandbox.io/embed/o952xlqq9y)

### 先决条件

本教程假设您非常熟悉 JavaScript，并且已经熟悉 React 框架。如果不是这样，您可以查看 [React 文档](https://reactjs.org/docs/)以了解更多关于 React 的信息。

在开始之前，您需要确保您的机器上已经安装了[节点](https://nodejs.org/en/)。建议您在机器上安装[纱线](https://yarnpkg.com/)包管理器，因为它将代替 Node 附带的 [npm](https://npmjs.com/) 。您可以按照本[纱线安装指南](https://yarnpkg.com/lang/en/docs/install/)在您的机器上安装纱线。

React 应用程序的样板代码将使用 [create-react-app](https://github.com/facebook/create-react-app) 包创建。您还需要确保它已全局安装在您的计算机上。如果你正在使用 npm > = 5.2，那么你不需要安装 create-react-app 作为一个全局依赖——你可以使用 npx 命令。

[![LogRocket Free Trial Banner](../Images/4aa67f42a82d61c79b61acb13eae9479.png)T2】](https://logrocket.com/signup/)

### 入门

#### 创建新应用

使用以下命令启动一个新的 React 应用程序。您可以随意命名该应用程序。

```
create-react-app react-datepicker 
```

> *T2`npm >= 5.2`*
> 
> 如果您使用的是 NPM 5.2 版或更高版本，它附带了一个额外的 npx 二进制文件。使用 npx 二进制文件，您不需要在您的机器上全局安装 create-react-app。您可以使用这个简单的命令启动一个新的 React 应用程序:
> 
> *T2`npx create-react-app react-datepicker`*

#### 安装依赖项

这个应用程序的依赖关系尽可能保持精简。运行以下命令安装所需的依赖项。

```
yarn add bootstrap reactstrap styled-components prop-types 
```

#### 包含自举 CSS

bootstrap 包已经作为应用程序的依赖项安装，以提供一些默认样式。要包含 Bootstrap 4 样式，请编辑 src/index.js 文件，并在每隔一条 import 语句之前添加以下行。

```
import "bootstrap/dist/css/bootstrap.min.css"; 
```

#### 目录设置

对于这种应用，需要两个主要组件。

1.  日历组件，它呈现带有日期选择功能的自定义日历。
2.  Datepicker 组件，该组件呈现日期输入，并显示用于挑选日期的日历。

这些组件中的每一个都包含在它自己的目录中，有两个文件:index.js 和 styles.js。index . js 文件导出组件，而 styles . js 文件导出组件所需的样式组件，以添加一些样式。

继续从项目根目录运行以下命令来创建组件目录和文件:

```
# Create directories
mkdir -p src/components/Calendar src/components/Datepicker

# Create files
(cd src/components/Calendar && touch index.js styles.js)
(cd src/components/Datepicker && touch index.js styles.js) 
```

因为在这个应用程序中处理日期不需要外部依赖，所以需要日期处理助手函数。运行以下命令为 calendar helper 模块创建一个文件。

```
mkdir -p src/helpers
touch src/helpers/calendar.js 
```

#### 启动应用程序

使用 yarn 在终端上运行以下命令来启动应用程序:

```
yarn start 
```

应用程序现在已经启动，可以开始开发了。请注意，已经为您打开了一个浏览器标签，带有*实时重新加载*功能，以便在您开发时与应用程序中的更改保持同步。

### 日历助手模块

#### 基本常量和助手

首先，定义一些构建日历所需的日历助手和常量。这些助手和常量将在您之前创建的日历助手模块中定义，并将作为名为 exports 的*导出。*

将以下内容添加到`src/helpers/calendar.js`文件中。

```
// (int) The current year
export const THIS_YEAR = +(new Date().getFullYear());

// (int) The current month starting from 1 - 12
// 1 => January, 12 => December
export const THIS_MONTH = +(new Date().getMonth()) + 1;

// Week days names and shortnames
export const WEEK_DAYS = {
  Sunday: "Sun",
  Monday: "Mon",
  Tuesday: "Tue",
  Wednesday: "Wed",
  Thursday: "Thu",
  Friday: "Fri",
  Saturday: "Sat"
}

// Calendar months names and shortnames
export const CALENDAR_MONTHS = {
  January: "Jan",
  February: "Feb",
  March: "Mar",
  April: "Apr",
  May: "May",
  June: "Jun",
  July: "Jul",
  August: "Aug",
  September: "Sep",
  October: "Oct",
  November: "Nov",
  December: "Dec"
}

// Weeks displayed on calendar
export const CALENDAR_WEEKS = 6;

// Pads a string value with leading zeroes(0) until length is reached
// For example: zeroPad(5, 2) => "05"
export const zeroPad = (value, length) => {
  return `${value}`.padStart(length, '0');
}

// (int) Number days in a month for a given year from 28 - 31
export const getMonthDays = (month = THIS_MONTH, year = THIS_YEAR) => {
  const months30 = [4, 6, 9, 11];
  const leapYear = year % 4 === 0;

  return month === 2
    ? leapYear
      ? 29
      : 28
    : months30.includes(month)
      ? 30
      : 31;
}

// (int) First day of the month for a given year from 1 - 7
// 1 => Sunday, 7 => Saturday
export const getMonthFirstDay = (month = THIS_MONTH, year = THIS_YEAR) => {
  return +(new Date(`${year}-${zeroPad(month, 2)}-01`).getDay()) + 1;
} 
```

这个代码片段包含解释每个助手函数的注释。然而，有几件事值得指出。

首先，像 Date.prototype 中的 getDay()和 getMonth()这样的方法通常返回一个从零开始的值。因此，一年的第一个月(一月)是 0，而十二月是 11，而一周的第一天(星期日)是 0，星期六是 7。

在前面的代码片段中，您会看到 1 总是被添加到这些从零开始的值中，因此对于工作日，星期日变为 1，对于月份，十二月变为 12。

另外，请注意`CALENDAR_WEEKS`被设置为 6。由于一个月通常跨越 4 周，这允许日历至少容纳上个月的最后一周和下个月的第一周。您将很快看到该常量的效果，因为它将用于日历构建器功能中。

#### 附加助手

将以下内容添加到`src/helpers/calendar.js`文件中，为日历模块添加一些额外的帮助函数。

```
// (bool) Checks if a value is a date - this is just a simple check
export const isDate = date => {
  const isDate = Object.prototype.toString.call(date) === '[object Date]';
  const isValidDate = date && !Number.isNaN(date.valueOf());

  return isDate && isValidDate;
}

// (bool) Checks if two date values are of the same month and year
export const isSameMonth = (date, basedate = new Date()) => {

  if (!(isDate(date) && isDate(basedate))) return false;

  const basedateMonth = +(basedate.getMonth()) + 1;
  const basedateYear = basedate.getFullYear();

  const dateMonth = +(date.getMonth()) + 1;
  const dateYear = date.getFullYear();

  return (+basedateMonth === +dateMonth) && (+basedateYear === +dateYear);

}

// (bool) Checks if two date values are the same day
export const isSameDay = (date, basedate = new Date()) => {

  if (!(isDate(date) && isDate(basedate))) return false;

  const basedateDate = basedate.getDate();
  const basedateMonth = +(basedate.getMonth()) + 1;
  const basedateYear = basedate.getFullYear();

  const dateDate = date.getDate();
  const dateMonth = +(date.getMonth()) + 1;
  const dateYear = date.getFullYear();

  return (+basedateDate === +dateDate) && (+basedateMonth === +dateMonth) && (+basedateYear === +dateYear);

}

// (string) Formats the given date as YYYY-MM-DD
// Months and Days are zero padded
export const getDateISO = (date = new Date) => {

  if (!isDate(date)) return null;

  return [
    date.getFullYear(),
    zeroPad(+date.getMonth() + 1, 2),
    zeroPad(+date.getDate(), 2)
  ].join('-');

}

// ({month, year}) Gets the month and year before the given month and year
// For example: getPreviousMonth(1, 2000) => {month: 12, year: 1999}
// while: getPreviousMonth(12, 2000) => {month: 11, year: 2000}
export const getPreviousMonth = (month, year) => {
  const prevMonth = (month > 1) ? month - 1 : 12;
  const prevMonthYear = (month > 1) ? year : year - 1;

  return { month: prevMonth, year: prevMonthYear };
}

// ({month, year}) Gets the month and year after the given month and year
// For example: getNextMonth(1, 2000) => {month: 2, year: 2000}
// while: getNextMonth(12, 2000) => {month: 1, year: 2001}
export const getNextMonth = (month, year) => {
  const nextMonth = (month < 12) ? month + 1 : 1;
  const nextMonthYear = (month < 12) ? year : year + 1;

  return { month: nextMonth, year: nextMonthYear };
} 
```

#### 默认导出

最后，日历助手模块的默认导出—**日历构建器函数**本身。该函数将月份和年份作为参数，并返回一个包含 42 个元素的数组，每个元素代表一个格式为[YYYY，MM，DD]的日历日期。

这是日历生成器功能。将这段代码片段附加到`src/helpers/calendar.js`文件中。

```
// Calendar builder for a month in the specified year
// Returns an array of the calendar dates.
// Each calendar date is represented as an array => [YYYY, MM, DD]

export default (month = THIS_MONTH, year = THIS_YEAR) => {

  // Get number of days in the month and the month's first day

  const monthDays = getMonthDays(month, year);
  const monthFirstDay = getMonthFirstDay(month, year);

  // Get number of days to be displayed from previous and next months
  // These ensure a total of 42 days (6 weeks) displayed on the calendar

  const daysFromPrevMonth = monthFirstDay - 1;
  const daysFromNextMonth = (CALENDAR_WEEKS * 7) - (daysFromPrevMonth + monthDays);

  // Get the previous and next months and years

  const { month: prevMonth, year: prevMonthYear } = getPreviousMonth(month, year);
  const { month: nextMonth, year: nextMonthYear } = getNextMonth(month, year);

  // Get number of days in previous month
  const prevMonthDays = getMonthDays(prevMonth, prevMonthYear);

  // Builds dates to be displayed from previous month

  const prevMonthDates = [...new Array(daysFromPrevMonth)].map((n, index) => {
    const day = index + 1 + (prevMonthDays - daysFromPrevMonth);
    return [ prevMonthYear, zeroPad(prevMonth, 2), zeroPad(day, 2) ];
  });

  // Builds dates to be displayed from current month

  const thisMonthDates = [...new Array(monthDays)].map((n, index) => {
    const day = index + 1;
    return [year, zeroPad(month, 2), zeroPad(day, 2)];
  });

  // Builds dates to be displayed from next month

  const nextMonthDates = [...new Array(daysFromNextMonth)].map((n, index) => {
    const day = index + 1;
    return [nextMonthYear, zeroPad(nextMonth, 2), zeroPad(day, 2)];
  });

  // Combines all dates from previous, current and next months
  return [ ...prevMonthDates, ...thisMonthDates, ...nextMonthDates ];

} 
```

请注意，生成器在数组中返回的日历日期从上个月最后一周的日期开始，经过给定月份的日期，一直到下个月第一周的日期。

### 日历组件

#### 构建日历组件

现在您有了日历助手模块，是时候构建日历 React 组件了。将下面的代码片段添加到`src/components/Calendar/index.js`文件中。

```
import React, { Component, Fragment } from "react";
import PropTypes from "prop-types";
import * as Styled from "./styles";
import calendar, {
  isDate,
  isSameDay,
  isSameMonth,
  getDateISO,
  getNextMonth,
  getPreviousMonth,
  WEEK_DAYS,
  CALENDAR_MONTHS
} from "../../helpers/calendar";

class Calendar extends Component {

  state = { ...this.resolveStateFromProp(), today: new Date() };

  resolveStateFromDate(date) {
    const isDateObject = isDate(date);
    const _date = isDateObject ? date : new Date();

    return {
      current: isDateObject ? date : null,
      month: +_date.getMonth() + 1,
      year: _date.getFullYear()
    };
  }

  resolveStateFromProp() {
    return this.resolveStateFromDate(this.props.date);
  }

  getCalendarDates = () => {
    const { current, month, year } = this.state;
    const calendarMonth = month || +current.getMonth() + 1;
    const calendarYear = year || current.getFullYear();

    return calendar(calendarMonth, calendarYear);
  };

  render() {
    return (
      <Styled.CalendarContainer>

        { this.renderMonthAndYear() }

        <Styled.CalendarGrid>
          <Fragment>
            { Object.keys(WEEK_DAYS).map(this.renderDayLabel) }
          </Fragment>

          <Fragment>
            { this.getCalendarDates().map(this.renderCalendarDate) }
          </Fragment>
        </Styled.CalendarGrid>

      </Styled.CalendarContainer>
    );
  }
}

Calendar.propTypes = {
  date: PropTypes.instanceOf(Date),
  onDateChanged: PropTypes.func
}

export default Calendar; 
```

请注意，在这段代码中，默认的日历导出以及其他助手函数和常量都是从日历助手模块中导入的。此外，calendar styles 模块的所有导出都是用 Styled 名称空间导入的。

虽然目前还没有创建样式，但是很快就会使用样式组件包创建样式。

使用 resolveStateFromProp()方法从 props 部分解析组件状态，该方法返回包含以下内容的对象:

*   current 是当前选定日期的日期对象或 null。
*   月份—如果设置了当前选定日期，则为该日期的月份，否则为当前日期的月份(今天)。
*   年份—如果设置了当前选定日期，则为该日期的年份，否则为当前日期(今天)的年份。

月和年状态属性是正确呈现日历所必需的，如 getCalendarDates()方法所示，该方法使用日历生成器函数来构建月和年的日历。

最后，用 today 属性增加了状态，该属性是当前日期的日期对象。

#### 渲染部分日历组件

在前面的日历组件代码片段中，render()方法引用了其他一些方法来呈现月份和年份、星期和日历日期。

将这些方法添加到日历组件中，如下面的代码片段所示。

```
class Calendar extends Component {

  // Render the month and year header with arrow controls
  // for navigating through months and years
  renderMonthAndYear = () => {
    const { month, year } = this.state;

    // Resolve the month name from the CALENDAR_MONTHS object map
    const monthname = Object.keys(CALENDAR_MONTHS)[
      Math.max(0, Math.min(month - 1, 11))
    ];

    return (
      <Styled.CalendarHeader>

        <Styled.ArrowLeft
          onMouseDown={this.handlePrevious}
          onMouseUp={this.clearPressureTimer}
          title="Previous Month"
        />

        <Styled.CalendarMonth>
          {monthname} {year}
        </Styled.CalendarMonth>

        <Styled.ArrowRight
          onMouseDown={this.handleNext}
          onMouseUp={this.clearPressureTimer}
          title="Next Month"
        />

      </Styled.CalendarHeader>
    );
  }

  // Render the label for day of the week
  // This method is used as a map callback as seen in render()
  renderDayLabel = (day, index) => {
    // Resolve the day of the week label from the WEEK_DAYS object map
    const daylabel = WEEK_DAYS[day].toUpperCase();

    return (
      <Styled.CalendarDay key={daylabel} index={index}>
        {daylabel}
      </Styled.CalendarDay>
    );
  }

  // Render a calendar date as returned from the calendar builder function
  // This method is used as a map callback as seen in render()
  renderCalendarDate = (date, index) => {
    const { current, month, year, today } = this.state;
    const _date = new Date(date.join("-"));

    // Check if calendar date is same day as today
    const isToday = isSameDay(_date, today);

    // Check if calendar date is same day as currently selected date
    const isCurrent = current && isSameDay(_date, current);

    // Check if calendar date is in the same month as the state month and year
    const inMonth = month && year && isSameMonth(_date, new Date([year, month, 1].join("-")));

    // The click handler
    const onClick = this.gotoDate(_date);

    const props = { index, inMonth, onClick, title: _date.toDateString() };

    // Conditionally render a styled date component
    const DateComponent = isCurrent
      ? Styled.HighlightedCalendarDate
      : isToday
        ? Styled.TodayCalendarDate
        : Styled.CalendarDate;

    return (
      <DateComponent key={getDateISO(_date)} {...props}>
        {_date.getDate()}
      </DateComponent>
    );
  }

} 
```

在 renderMonthAndYear()方法中，首先从 CALENDAR_MONTHS 对象中解析月份名称。然后，它被呈现在年份旁边，并且在左侧和右侧有两个箭头控件，用于在月份和年份之间导航。

每个箭头控件都有 mousedown 和 mouseup 事件的事件处理程序，稍后将定义这些事件处理程序— handlePrevious()、handleNext()和 clearPressureTimer()。

renderMonthAndYear()方法呈现的 DOM 类似于下面的屏幕截图(带有一些样式):

[![](../Images/9b02673791e4a6b33ba2d100e581c516.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RgPUL-PE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/788/0%2ApkHRL-f8Jtgz8vvS.png)

renderDayLabel()方法呈现一周中某一天的标签。它从 WEEK_DAYS 对象中解析标签。注意，它有两个参数——day 和 index，因为它被用作。在 render()方法中看到的 map()。

映射之后，这是一周中每一天呈现的 DOM 的样子。

[![](../Images/1d5a61211b85e459cb080e0313a6afd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L9NRnrFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/804/0%2AMOCVDGkwn5Lm09qO.png)

renderCalendarDate()方法也用作. map()回调函数，并呈现日历日期。它作为第一个参数接收的日期格式为[YYYY，MM，DD]。

它检查日期是否与今天相同，是否与当前选择的日期相同，是否与当前状态的月份和年份在同一个月。通过这些检查，它有条件地呈现日历日期单元格的一个变体— HiglightedCalendarDate、TodayCalendarDate 或 calendar date。

还要注意，为每个呈现的日历日期设置了 onClick 处理程序，以便使用 gotoDate()方法跳转到该特定日期，该方法将在下一节中定义。

#### 事件处理程序

在前面几节中已经提到了一些事件处理程序。继续更新日历组件，为事件处理程序添加以下代码片段。

```
class Calendar extends Component {

  gotoDate = date => evt => {
    evt && evt.preventDefault();
    const { current } = this.state;
    const { onDateChanged } = this.props;

    !(current && isSameDay(date, current)) &&
      this.setState(this.resolveStateFromDate(date), () => {
        typeof onDateChanged === "function" && onDateChanged(date);
      });
  }

  gotoPreviousMonth = () => {
    const { month, year } = this.state;
    this.setState(getPreviousMonth(month, year));
  }

  gotoNextMonth = () => {
    const { month, year } = this.state;
    this.setState(getNextMonth(month, year));
  }

  gotoPreviousYear = () => {
    const { year } = this.state;
    this.setState({ year: year - 1 });
  }

  gotoNextYear = () => {
    const { year } = this.state;
    this.setState({ year: year + 1 });
  }

  handlePressure = fn => {
    if (typeof fn === "function") {
      fn();
      this.pressureTimeout = setTimeout(() => {
        this.pressureTimer = setInterval(fn, 100);
      }, 500);
    }
  }

  clearPressureTimer = () => {
    this.pressureTimer && clearInterval(this.pressureTimer);
    this.pressureTimeout && clearTimeout(this.pressureTimeout);
  }

  handlePrevious = evt => {
    evt && evt.preventDefault();
    const fn = evt.shiftKey ? this.gotoPreviousYear : this.gotoPreviousMonth;
    this.handlePressure(fn);
  }

  handleNext = evt => {
    evt && evt.preventDefault();
    const fn = evt.shiftKey ? this.gotoNextYear : this.gotoNextMonth;
    this.handlePressure(fn);
  }

} 
```

gotoDate()方法是一个高阶函数，它将一个 Date 对象作为其参数，并返回一个事件处理程序，可以触发该事件处理程序来更新状态中当前选定的日期。请注意，resolveStateFromDate()方法用于根据日期解析月和年，并更新状态。

如果将回调函数传递给日历组件的 onDateChanged 属性，那么将使用更新后的日期调用该函数。这对于您想要将日期更改传播到父组件的情况非常有用。

handlePrevious()和 handleNext()事件处理程序具有相似的行为。默认情况下，它们在几个月中循环。然而，如果按下了 shift 键，那么它们会循环显示年份。最后，它们将控制权移交给 handlePressure()方法。

handlePressure()方法简单地使用计时器来模拟快速循环几个月或几年的压力点击，而 clearPressureTimer()方法清除这些计时器。

#### 组件生命周期方法

日历组件离完成只差一些生命周期方法了。下面是日历组件的生命周期方法。

```
class Calendar extends Component {

  // ... other methods here

  componentDidMount() {
    const now = new Date();
    const tomorrow = new Date().setHours(0, 0, 0, 0) + 24 * 60 * 60 * 1000;
    const ms = tomorrow - now;

    this.dayTimeout = setTimeout(() => {
      this.setState({ today: new Date() }, this.clearDayTimeout);
    }, ms);
  }

  componentDidUpdate(prevProps) {
    const { date, onDateChanged } = this.props;
    const { date: prevDate } = prevProps;
    const dateMatch = date == prevDate || isSameDay(date, prevDate);

    !dateMatch &&
      this.setState(this.resolveStateFromDate(date), () => {
        typeof onDateChanged === "function" && onDateChanged(date);
      });
  }

  clearDayTimeout = () => {
    this.dayTimeout && clearTimeout(this.dayTimeout);
  }

  componentWillUnmount() {
    this.clearPressureTimer();
    this.clearDayTimeout();
  }

} 
```

在 componentDidMount()方法中，有一个日期计时器，它被设置为在当天结束时自动将 today state 属性更新到第二天。

在卸载组件之前，所有的计时器都被清除，如 componentWillUnmount()方法所示。

#### 日历样式

现在您已经完成了 Calendar 组件，接下来您将继续创建为日历添加样式所需的样式化组件。

将下面的代码片段添加到`src/components/Calendar/styles.js`文件中。

```
import styled from 'styled-components';

export const Arrow = styled.button`
  appearance: none;
  user-select: none;
  outline: none !important;
  display: inline-block;
  position: relative;
  cursor: pointer;
  padding: 0;
  border: none;
  border-top: 1.6em solid transparent;
  border-bottom: 1.6em solid transparent;
  transition: all .25s ease-out;
`;

export const ArrowLeft = styled(Arrow)`
  border-right: 2.4em solid #ccc;
  left: 1.5rem;
  :hover {
    border-right-color: #06c;
  }
`;

export const ArrowRight = styled(Arrow)`
  border-left: 2.4em solid #ccc;
  right: 1.5rem;
  :hover {
    border-left-color: #06c;
  }
`;

export const CalendarContainer = styled.div`
  font-size: 5px;
  border: 2px solid #06c;
  border-radius: 5px;
  overflow: hidden;
`;

export const CalendarHeader = styled.div`
  display: flex;
  align-items: center;
  justify-content: space-between;
`;

export const CalendarGrid = styled.div`
  display: grid;
  grid-template: repeat(7, auto) / repeat(7, auto);
`;

export const CalendarMonth = styled.div`
  font-weight: 500;
  font-size: 5em;
  color: #06c;
  text-align: center;
  padding: 0.5em 0.25em;
  word-spacing: 5px;
  user-select: none;
`;

export const CalendarCell = styled.div`
  text-align: center;
  align-self: center;
  letter-spacing: 0.1rem;
  padding: 0.6em 0.25em;
  user-select: none;
  grid-column: ${props => (props.index % 7) + 1} / span 1;
`;

export const CalendarDay = styled(CalendarCell)`
  font-weight: 600;
  font-size: 2.25em;
  color: #06c;
  border-top: 2px solid #06c;
  border-bottom: 2px solid #06c;
  border-right: ${props => (props.index % 7) + 1 === 7 ? `none` : `2px solid #06c`};
`;

export const CalendarDate = styled(CalendarCell)`
  font-weight: ${props => props.inMonth ? 500 : 300};
  font-size: 4em;
  cursor: pointer;
  border-bottom: ${props => ((props.index + 1) / 7) <= 5 ? `1px solid #ddd` : `none`};
  border-right: ${props => (props.index % 7) + 1 === 7 ? `none` : `1px solid #ddd`};
  color: ${props => props.inMonth ? `#333` : `#ddd`};
  grid-row: ${props => Math.floor(props.index / 7) + 2} / span 1;
  transition: all .4s ease-out;
  :hover {
    color: #06c;
    background: rgba(0, 102, 204, 0.075);
  }
`;

export const HighlightedCalendarDate = styled(CalendarDate)`
  color: #fff !important;
  background: #06c !important;
  position: relative;
  ::before {
    content: '';
    position: absolute;
    top: -1px;
    left: -1px;
    width: calc(100% + 2px);
    height: calc(100% + 2px);
    border: 2px solid #06c;
  }
`;

export const TodayCalendarDate = styled(HighlightedCalendarDate)`
  color: #06c !important;
  background: transparent !important;
  ::after {
    content: '';
    position: absolute;
    right: 0;
    bottom: 0;
    border-bottom: 0.75em solid #06c;
    border-left: 0.75em solid transparent;
    border-top: 0.75em solid transparent;
  }
  :hover {
    color: #06c !important;
    background: rgba(0, 102, 204, 0.075) !important;
  }
`; 
```

这就是正确呈现日历所需的组件和样式。如果此时在应用程序中渲染日历组件，它应该看起来像这个截图。

[![](../Images/cf93389bef28afbfc35234ea972d84cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kGhZUcIx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AokAZnzvskWp0uOzk.png)

### 日期选择器组件

#### 构建日期选择器

要开始构建 Datepicker 组件，请将以下代码片段添加到`src/components/Datepicker/index.js`文件中。

```
import React from "react";
import PropTypes from "prop-types";
import Calendar from "../Calendar";
import * as Styled from "./styles";
import { isDate, getDateISO } from "../../helpers/calendar";

class Datepicker extends React.Component {

  state = { date: null, calendarOpen: false }

  toggleCalendar = () => this.setState({ calendarOpen: !this.state.calendarOpen })

  handleChange = evt => evt.preventDefault()

  handleDateChange = date => {
    const { onDateChanged } = this.props;
    const { date: currentDate } = this.state;
    const newDate = date ? getDateISO(date) : null;

    currentDate !== newDate &&
      this.setState({ date: newDate, calendarOpen: false }, () => {
        typeof onDateChanged === "function" && onDateChanged(this.state.date);
      });
  }

  componentDidMount() {
    const { value: date } = this.props;
    const newDate = date && new Date(date);

    isDate(newDate) && this.setState({ date: getDateISO(newDate) });
  }

  componentDidUpdate(prevProps) {
    const { value: date } = this.props;
    const { value: prevDate } = prevProps;
    const dateISO = getDateISO(new Date(date));
    const prevDateISO = getDateISO(new Date(prevDate));

    dateISO !== prevDateISO && this.setState({ date: dateISO });
  }

}

Datepicker.propTypes = {
  label: PropTypes.string,
  value: PropTypes.string,
  onDateChanged: PropTypes.func
}

export default Datepicker; 
```

这里，组件状态用两个属性初始化:

*   日期—日期选择器上当前日期的 ISO 字符串表示形式。格式为“YYYY-MM-DD”。
*   calendarOpen 一个布尔标志，指示日期选取器日历是否可见。

当组件挂载时，Date 对象从传递给组件的值 prop 中解析出来，并根据状态进行更新，如 componentDidMount()方法所示。

handleDateChange()方法将 Date 对象作为其参数，并更新状态中的日期。如果将回调函数传递给 Datepicker 组件的 onDateChanged 属性，那么将使用更新的 ISO 日期字符串调用该函数。

#### 渲染日期选择器

此时，值得一提的是，Bootstrap Dropdown 组件将用于模拟自定义日期选择器的下拉效果。这就是为什么 [**Reactstrap**](https://reactstrap.github.io/) 包被添加为这个项目的依赖项的原因。

您很快就会注意到，呈现在日期选择器中的样式化组件是来自 [**Reactstrap**](https://reactstrap.github.io/) 的下拉组件的样式化扩展。

更新 Datepicker 组件以包含 render()方法，如下面的代码片段所示。

```
class Datepicker extends React.Component {

  // ... other methods here

  render() {
    const { label } = this.props;
    const { date, calendarOpen } = this.state;

    return (
      <Styled.DatePickerContainer>

        <Styled.DatePickerFormGroup>

          <Styled.DatePickerLabel>{label || 'Enter Date'}</Styled.DatePickerLabel>

          <Styled.DatePickerInput
            type="text"
            value={date ? date.split("-").join(" / ") : ""}
            onChange={this.handleChange}
            readOnly="readonly"
            placeholder="YYYY / MM / DD"
          />

        </Styled.DatePickerFormGroup>

        <Styled.DatePickerDropdown isOpen={calendarOpen} toggle={this.toggleCalendar}>

          <Styled.DatePickerDropdownToggle color="transparent" />

          <Styled.DatePickerDropdownMenu>
            { calendarOpen && (
              <Calendar date={date && new Date(date)} onDateChanged={this.handleDateChange} />
            )}
          </Styled.DatePickerDropdownMenu>

        </Styled.DatePickerDropdown>

      </Styled.DatePickerContainer>
    );
  }

} 
```

在这里，风格。DatePickerFormGroup 组件是一个引导程序。包装日期选择器标签和输入字段的表单组。值得注意的是，输入字段属于“文本”类型，并且标记为只读，因此不能直接编辑。另请注意，输入元素上 change 事件的默认行为已被阻止。

风格的。DatePickerDropdown 组件及其后代是 Reactstrap 包中 Dropdown 组件的样式扩展。你可以在 Reactstrap [这里](https://reactstrap.github.io/components/dropdowns/)了解更多关于下拉的信息。

最后，日历组件呈现在下拉菜单中，传递来自状态的日期和 handleDateChange()方法作为 onDateChanged 属性的回调函数。

Datepicker 组件的最终呈现的 DOM 应该类似于下面的屏幕截图(带有一些样式):

[![](../Images/6de48bf8bde56916bdd46096cb245c3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0V3uNkd8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AVZmq6zHfYiuw5Fp3.png)

#### 设置日期选择器的样式

将下面的代码片段添加到`src/components/Datepicker/styles.js`文件中，以创建日期选择器所需的样式化组件。

```
import styled from 'styled-components';
import { FormGroup, Label, Input, Dropdown, DropdownToggle, DropdownMenu } from 'reactstrap';

export const DatePickerContainer = styled.div`
  position: relative;
`;

export const DatePickerFormGroup = styled(FormGroup)`
  display: flex;
  justify-content: space-between;
  position: relative;
  width: 100%;
  border: 2px solid #06c;
  border-radius: 5px;
  overflow: hidden;
`;

export const DatePickerLabel = styled(Label)`
  margin: 0;
  padding: 0 2rem;
  font-weight: 600;
  font-size: 0.7rem;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: #06c;
  border-right: 2px solid #06c;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 102, 204, 0.05);
`;

export const DatePickerInput = styled(Input)`
  padding: 1rem 2rem;
  font-weight: 500;
  font-size: 1rem;
  color: #333;
  box-shadow: none;
  border: none;
  text-align: center;
  letter-spacing: 1px;
  background: transparent !important;
  display: flex;
  align-items: center;

  ::placeholder {
    color: #999;
    font-size: 0.9rem;
  }
`;

export const DatePickerDropdown = styled(Dropdown)`
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
`;

export const DatePickerDropdownToggle = styled(DropdownToggle)`
  position: relative;
  width: 100%;
  height: 100%;
  background: transparent;
  opacity: 0;
  filter: alpha(opacity=0);
`;

export const DatePickerDropdownMenu = styled(DropdownMenu)`
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  border: none;
  padding: 0;
  margin: 0;
  transform: none !important;
`; 
```

### app 组件

最后，更新`src/App.js`文件，看起来像下面的代码片段。

```
import React, { Component } from "react";
import Datepicker from "./components/Datepicker";

class App extends Component {
  render() {
    return (

    );
  }
}

export default App; 
```

如果您按照本文和代码片段进行了操作，您应该在 React 应用程序中呈现了一个可工作的自定义日期选择器。

### 结论

在本教程中，您已经能够逐步了解如何构建一个自定义的 React 日期选取器组件，该组件可用于替换本机 HTML5 日期选取器输入元素。

尽管在本教程中创建的自定义日期选取器按预期工作，但它并不完全满足日期选取器元素的所有要求。可以进行进一步的改进，例如:

*   通过 props 实现最大和最小日期
*   将输入类型从“文本”切换到“日期”
*   更好的可访问性改进

你可以在 GitHub 的[**react-date picker-demo**](https://github.com/gladchinda/react-datepicker-demo)获得这个定制日期选择器的更完善版本的完整源代码。你也可以在代码沙箱这里获得现场演示[。](https://codesandbox.io/s/o952xlqq9y)

#### 拍拍&跟着

如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/u/ddcd0e9719e5) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

***享受编码……***

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](../Images/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[如何用 React](https://blog.logrocket.com/react-datepicker-217b4aa840da/) 构建一个定制的日期选择器首先出现在[的博客](https://blog.logrocket.com)上。