# 设计系统实用程序简介

> 原文：<https://dev.to/mrmartineau/introducing-design-system-utils--3mp7>

维护 web 应用程序(或 React Native)的样式一致性通常很困难。没有管理共享值和设置的通用方法。这个微框架旨在标准化你的设计系统，并提供有用的工具来访问它的信息。它不仅仅是特定结构中的一组变量，它还包括一些函数，可以更方便地访问设计系统中的值。

## 让我简单介绍一下基本知识

你首先需要创建你的设计系统文件，它包含了你的应用程序将要用到的所有全局变量，比如字体大小，调色板，间距等等。我一般会创建一个名为 theme 或者 designsystem 的顶级目录，在里面加一个 index.js，像这样:

```
// ./theme/index.js
import DesignSystem from 'design-system-utils'

// your design-system goes here, see below for details
export const myDesignSystem = {...}

export const ds = new DesignSystem(myDesignSystem, {
  useModularScale: true,
  fontSizeUnit: 'rem',
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 设计系统对象的形状是什么？

以下是您的设计系统应该使用的强制性项目。除了这些，你可以添加任何你喜欢的东西。

```
{
  type: {
    // this should be set as a px value if you have `options.fontSizeUnit` set
    // to 'rem' or 'em' so that the lib can convert the values properly
    baseFontSize: <string>,

    sizes: {
      key: <number | string>,
    },

    // If you're using a modular scale, set it up here
    // Use these docs to find out more: https://github.com/modularscale/modularscale-js
    modularscale: {
      base: <number | array>,  // should be the same as baseFontSize
      ratio: <number>,
    },
  },

  // Color palette
  // Each object needs to have the same shape
  // Each color object needs a `base` value to be the default
  // Have as many color objects as you like
  colors: {
    // Used with `ds.color('colorName')`
    colorPalette: {
      colorName: {
        base: <string>, // base is the default
      },
    },

    // Used with `ds.brand('colorName)`
    brand: {
      colorName: <string>, // base is the default
    }
  },

  // Breakpoints
  // Used with `ds.bp()`
  // Keys can be anything you like
  // Have as many breakpoints as you like
  // Values can be use any unit you like
  breakpoints: {
    key: <number | string>,
  },

  // Z-index
  // Used with `ds.z()`
  zIndex: {
    key: <number>
  },

  // Spacing
  // Used with `ds.spacing()` or `ds.space()`
  spacing: {
    scale: <array>[<number | string>, ...],
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

这是从示例设计系统中摘录的。在示例目录中可以看到更完整的示例。

```
export const myDesignSystem = {
  type: {
    baseFontSize: '20px',

    // the values below use modular-scale
    sizes: {
      xs: -2,
      s: -1,
      base: 0, // [default] p, h5, h6
      m: 1, // h4
      l: 2, // h3
      xl: 3, // h2
      xxl: 4, // h1
    },

    modularscale: {
      base: 20,
      ratio: 1.5,
    },

    fontFamily: {
      system:
        '-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, "Fira Sans", "Droid Sans"',
      sans: '"Helvetica Neue", Helvetica, Arial, sans-serif',
      serif: 'Georgia, "Times New Roman", Times, serif',
      mono: 'Menlo, Monaco, "Courier New", monospace',
    },

    lineHeight: {
      headings: 1.1,
    },

    fontWeight: {
      normal: 300, // Useful to set here if using anything other than `normal`
      bold: 'bold', // Useful to set here when bold webfonts come as 400 font-weight.
      headings: 'bold', // instead of browser default, bold
    },
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

## 访问应用程序中的设计系统数据

要访问你的设计系统，你只需要将它`import`到当前文件，就像这样:

```
import { ds } from './myDesignSystem' 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我使用设计系统和 [styled-components](https://styled-components.com) 创建了一个非常简单的组件，您应该能够看到从设计系统中获取信息是多么容易。

```
// Example uses styled-components
import styled from 'styled-component'
import { ds } from './theme'
export const Box = styled.div`
  font-family: ${ds.get('type.fontFamilyBase')};
  background-color: ${ds.brand('primary')};
  margin: ${ds.space(2)} 0;
` 
```

Enter fullscreen mode Exit fullscreen mode

## 选项

有两个选项可以传递给你的设计系统。这些与字体大小有关。

```
// Use default options
export const ds = new DesignSystem(myDesignSystem)

// With custom options
export const ds = new DesignSystem(myDesignSystem, {

  // converts the `type.sizes` values into modular scale values
  useModularScale: true,

  // sets the font-size unit when calling fs.fontSize()
  fontSizeUnit: 'rem',
}) 
```

Enter fullscreen mode Exit fullscreen mode

## API 方法

### 获得任意值

`ds.get()`函数可用于从设计系统中获取任何值。您可以使用点符号来查找任意深度的嵌套值。

```
// with the system setup, as above
ds.get('type.lineHeight.headings') // e.g. 1.1 
```

Enter fullscreen mode Exit fullscreen mode

我还提供了一些其他的帮助方法，使检索某些值变得更加简单。大多数都是 ds.get()方法的缩写。

### 字号

`ds.fontSize()`方法是`ds.get()`方法的简写，但是多了一点。它用于从 type.sizes 对象中提取值。

`type.sizes`对象的值可以用几种方式格式化:

*   `s: -2` —如果使用了一个数字，并且 options.modularscale = true，则 ds.fontSize()会将该数字转换为模刻度上的值。
*   `s: '13px'`
*   `s: '1.4rem'`

```
// define some values// type.sizes object
sizes: {
  xs: -2,
  s: -1,
  base: 0, // [default] p, h5, h6
  m: 1, // h4
  l: 2, // h3
  xl: 3, // h2
  xxl: 4, // h1
},

// retrieve some values

ds.fontSize('xl')
ds.fs('xl') // `fs()` is a short-hand alias for `fontSize()`
ds.fs('xl', true) // return font-size in px regardless of `option.fontSizeUnit` value
ds.fs(6) // returns font-size of the 6th item on the modular-scale. This will only work if the òptions.modularscale` is `true` 
```

Enter fullscreen mode Exit fullscreen mode

#### 模块秤

要使用模块化秤，需要做几件事:

*   设置`options.modularscale = true`，详见上文
*   在`type.modularscale`中定义您的模块化秤选项。设计系统实用程序使用 modularscale-js 进行转换。

```
modularscale: {
  base: 20,
  ratio: 1.5,
}, 
```

Enter fullscreen mode Exit fullscreen mode

### 调色板

有两种可能的方法来获取颜色信息:通过调色板和品牌颜色。调色板旨在包含您的应用程序将使用的所有颜色(及其阴影)，品牌调色板是您的品牌使用的特定颜色。有两种方法可以用来检索这些值，它们是:

```
colors: {
  // With a color palette like this:
  colorPalette: {
    bright: {
      base: '#F9FAFB',
      dark: '#F4F6F8',
      darker: '#DFE4E8',
    },

    dark: {
      base: '#212B35',
      light: '#454F5B',
      lighter: '#637381',
    },
  },

  // With a brand palette like this:
  brand: {
    red: '#e82219',
    deeporange: '#ff7200',
    orange: '#ff9500',
    green: '#c4d000',
    teal: '#1aa5c8',
    navy: '#0052da',
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 获取调色板值

`ds.color()`函数从 colorPalette 对象中获取值。它假设每种颜色都有一个基本属性和同一种颜色的不同色度的其他属性。这是 ds.get()函数的简写。

```
// Get values like this:
ds.color('bright') // #F9FAFB - the `base` key is the default, so it is not needed
ds.color('bright', 'dark') 
```

Enter fullscreen mode Exit fullscreen mode

### 获取品牌调色板值

`ds.brand()`函数从 colors.brand 对象中获取值。这是`ds.get()`功能的简写。

```
// Get brand values like this:
ds.brand('orange')
ds.brand('pink')
ds.brand('primary.blue') // it is possible to nest this object as much as you like 
```

Enter fullscreen mode Exit fullscreen mode

### 获取断点值

`ds.bp()`方法是`ds.get()`方法的简称。它可用于从断点对象中获取断点。

```
ds.bp('m') 
```

Enter fullscreen mode Exit fullscreen mode

### 获取 z-index 值

`ds.z()`方法是`ds.get()`方法的简称。它可用于从 zIndex 对象获取断点。

```
ds.z('low') 
```

Enter fullscreen mode Exit fullscreen mode

### 获取间距值

方法从 spacing.scale 数组中返回一个值。它获取该数组的索引，并将值转换为像素。

```
// Example scale array// scale: [0, 8, 16, 24, 32, 40]
ds.spacing(2) // '16px' 
```

Enter fullscreen mode Exit fullscreen mode

注意:`ds.space(2)`也可以。

### 计算

框架目前提供了几个计算函数，`multiply`、`toPx`、`pxTo`:

#### `multiply(initial, muliplier)`

```
ds.multiply(10, 2) // 20

// you can pass in another value from the system
ds.multiply(ds.get('spacing.baseline'), 2)

// or just use the key from the system
// the initial value will always be run through `parseFloat()`
ds.multiply('spacing.baseline', 2) 
```

Enter fullscreen mode Exit fullscreen mode

#### `pxTo(fontSize, baseFontSize, unit)`

将 px 转换为 rem 或 em

```
// ds.pxTo(fontSize, baseFontSize, unit)
ds.pxTo(12, 20, 'rem') // 0.6rem
ds.pxTo(12, 20, 'em') // 0.6em 
```

Enter fullscreen mode Exit fullscreen mode

#### `toPx(fontSize, baseFontSize)`

将 rem 或 em 值转换为 px

```
ds.toPx('1.875rem', 16) // 30px
ds.toPx('1.875em', 16) // 30px 
```

Enter fullscreen mode Exit fullscreen mode

## 演示&的例子

我在 codesandbox.io 上创建了一个演示，它包括使用带有情感、风格化组件和魅力的设计系统工具的例子。这里还有一个基本的例子。

## 怎么得到的？

您可以使用:
通过 npm 安装它

```
npm install --save design-system-utils

yarn add design-system-utils 
```

Enter fullscreen mode Exit fullscreen mode

## 如何投稿？

我一直在寻找改进的方法，所以欢迎任何和所有的反馈。代码托管在 github.com/mrmartineau/design-system-utils 的 GitHub 上