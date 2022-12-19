# 如何创建类似带有浮动标签和动画下划线的表单文本字段的材料设计

> 原文：<https://dev.to/teroauralinna/how-to-create-material-design-like-form-text-fields-with-floating-label-and-animated-underline-bar-483m>

这篇文章展示了如何用一个浮动标签和一个扩展的下划线动画来实现[材质设计](https://material.io)，比如表单[文本字段](https://material.io/design/components/text-fields.html)。这篇文章包括了使用和不使用 Javascript 如何设计文本字段样式的例子。

> 材料设计是一种视觉语言，它将优秀设计的经典原则与科技创新相结合。

*详见[material . io](https://material.io)T3】。*

如果您需要一套完整的现成组件，可以使用许多材料组件库和框架。

*   [材料组件](https://material.io/develop/)-Android、Flutter、iOS 和 web 的材料组件
*   [vue 化](https://vuetifyjs.com/en/)-vue . js 的材料组件框架
*   [Vue 材料](https://vuematerial.io)-Vue . js 的材料设计
*   [角形材料](https://material.angular.io) -角形材料设计成分
*   [MATERIAL-UI](https://material-ui.com) - React 实现谷歌材质设计的组件
*   基于材料设计的现代响应前端框架

如果你想自己实现材料设计，继续阅读。

## 用 Javascript 进行材料设计

以下演示是一个如何实现材质设计字段样式的基本示例。演示不包含错误处理，验证等。Javascript 用于在表单字段被聚焦或填充值时改变字段的状态。

[https://codepen.io/teroauralinna/embed/rZZGpe?height=600&default-tab=result&embed-version=2](https://codepen.io/teroauralinna/embed/rZZGpe?height=600&default-tab=result&embed-version=2)

### 超文本标记语言

让我们从添加输入和文本区域字段标记开始。

#### 输入栏

```
<div class="form-field">
  <div class="form-field__control">
    <label for="exampleField" class="form-field__label">Example field</label>
    <input id="exampleField" type="text" class="form-field__input" />
  </div>
</div> 
```

#### 文本区域字段

```
<div class="form-field">
  <div class="form-field__control">
    <label for="exampleTextarea" class="form-field__label">Example textarea</label>
    <textarea id="exampleTextarea" class="form-field__textarea"></textarea>
  </div>
</div> 
```

### 风格

在演示中，我使用了[引导重启](https://getbootstrap.com/docs/4.1/content/reboot/)。如果使用其他东西作为样式基线，样式可能需要调整。

动画使用`translate`和`scale`变换规则，这些规则使用非常简单，支持良好，性能良好。

```
$primary-color: #b11adc;
$animation-duration: 0.4s;

@mixin label-active() {
  font-size: 0.75rem;
  transform: translateY(-14px);
}

.form-field {
  display: block;
  margin-bottom: 16px;

  &--is-active {
    .form-field__control {
      &::after {
        border-bottom: 2px solid $primary-color;
        transform: scaleX(150);
      }
    }
    .form-field__label {
      color: #b11adc;
      @include label-active();
    }
  }
  &--is-filled {
    .form-field__label {
      @include label-active();
    }
  }
}
.form-field__label {
  display: block;
  font-size: 1.2rem;
  font-weight: normal;
  left: 0;
  margin: 0;
  padding: 18px 12px 0 ;
  position: absolute;
  top: 0;
  transition: all $animation-duration;
  width: 100%;
}
.form-field__control {
  background: #eee;
  border-radius: 8px 8px 0 0;
  overflow: hidden;
  position: relative;
  width: 100%;

  &::after {
    border-bottom: 2px solid $primary-color;
    bottom: 0;
    content: "";
    display: block;
    left: 0;
    margin: 0 auto;
    position: absolute;
    right: 0;
    transform: scaleX(0);
    transition: all $animation-duration;
    width: 1%;
  }
}
.form-field__input,
.form-field__textarea {
  appearance: none;
  background: transparent;
  border: 0;
  border-bottom: 1px solid #999;
  color: #333;
  display: block;
  font-size: 1.2rem;
  margin-top: 24px;
  outline: 0;
  padding: 0 12px 10px 12px;
  width: 100%;
}
.form-field__textarea {
  height: 150px;
} 
```

### 浮动标签和下划线栏动画的触发器

现在我们的表单域 HTML 标记和样式已经准备好了。我们仍然需要添加一点 Javascript 来改变字段的状态，无论字段是活动的还是非活动的。

下面的 ES6 示例可以被移植到您自己的项目中使用的任何 JS 框架或库。

```
const setActive = (el, active) => {
  const formField = el.parentNode.parentNode
  if (active) {
    formField.classList.add('form-field--is-active')
  } else {
    formField.classList.remove('form-field--is-active')
    el.value === '' ? 
      formField.classList.remove('form-field--is-filled') : 
      formField.classList.add('form-field--is-filled')
  }
}

[].forEach.call(
  document.querySelectorAll('.form-field__input, .form-field__textarea'),
  (el) => {
    el.onblur = () => {
      setActive(el, false)
    }
    el.onfocus = () => {
      setActive(el, true)
    }
  }
) 
```

## 用纯 CSS 方法实验材料设计

我们可以不用 Javascript 只用 CSS 来构建几乎完全相同的功能。微软 Edge 浏览器是我唯一一个没有正常工作的。

[https://codepen.io/teroauralinna/embed/JeKrXe?height=600&default-tab=result&embed-version=2](https://codepen.io/teroauralinna/embed/JeKrXe?height=600&default-tab=result&embed-version=2)

### 超文本标记语言

我们需要对 HTML 代码做一些修改。占位符属性必须添加到字段中。请注意，占位符必须包含至少一个空格。空了就没用了。

我们还需要交换输入和标签元素的顺序。还需要新的`form-field__bar` div 元素。然后，我们可以通过兄弟选择器定位元素。

#### 输入栏

```
<div class="form-field">
  <div class="form-field__control">
    <input id="exampleField" type="text" class="form-field__input" value="" placeholder=" " />
    <label for="exampleField" class="form-field__label">Example field</label>
    <div class="form-field__bar"></div>
  </div>
</div> 
```

#### 文本区域字段

```
<div class="form-field">
  <div class="form-field__control">
    <textarea id="exampleTextarea" class="form-field__textarea" placeholder=" "></textarea>
    <label for="exampleTextarea" class="form-field__label">Example textarea</label>
    <div class="form-field__bar"></div>
  </div>
</div> 
```

### 风格

没有 Javascript，样式几乎不需要大的改变。当我们使用 Javascript 时，我们可以给任何想要的元素添加一个状态修饰符类。但是当只使用 CSS 时，我们的选择非常有限。我们可以使用兄弟选择器来设计放在输入元素之后的元素。

聚焦字段可以使用`:focus`选择器。选择器 [`:placeholder-shown`](https://www.caniuse.com/#search=placeholder-shown) 似乎是将样式定位到具有值的字段的唯一方法。

Edge 不支持`:placeholder-shown`。有使用`:valid`选择器的实现，但是它在验证方面有缺点，所以我决定不使用它。

由于边缘问题，我颠倒了标签样式的顺序。默认情况下，标签现在处于“活动”状态，浮动标签动画仅在浏览器支持`:placeholder-shown`时使用。所以表单仍然可以使用，但是没有动画标签。

IE 11 甚至 IE 10 都可以用`:-ms-input-placeholder`选择器支持。

不能再使用`::after`伪元素，因为它不能应用于输入或文本区域字段。CSS 父选择器在这里会很有用。所以我创建了一个新的 div 元素来处理下划线栏动画。

以下 CSS 规则用于样式标签和下划线栏。

**输入字段包含文本时的标签**

```
.form-field__textarea:placeholder-shown ~ .form-field__label {
} 
```

**输入字段聚焦时的标签**

```
.form-field__textarea:focus ~ .form-field__label {
} 
```

**当输入字段被聚焦时，给条划线**

```
.form-field__textarea:focus ~ .form-field__bar {
} 
```

#### 完整的样式

```
$primary-color: #b11adc;
$animation-duration: 0.4s;

@mixin label-inactive() {
  font-size: 1.2rem;
  transform: translateY(0);
}

@mixin label-active() {
  font-size: 0.75rem;
  transform: translateY(-14px);
}

.form-field {
  display: block;
  margin-bottom: 16px;
}
.form-field__label {
  @include label-active();
  display: block;
  font-weight: normal;
  left: 0;
  margin: 0;
  padding: 18px 12px 0;
  position: absolute;
  top: 0;
  transition: all $animation-duration;
  width: 100%;
}
.form-field__control {
  background: #eee;
  border-radius: 8px 8px 0 0;
  overflow: hidden;
  position: relative;
  width: 100%;
}
.form-field__bar {
  border-bottom: 2px solid $primary-color;
  bottom: 0;
  content: "";
  display: block;
  left: 0;
  margin: 0 auto;
  position: absolute;
  right: 0;
  transform: scaleX(0);
  transition: all $animation-duration;
  width: 1%;
}
.form-field__input,
.form-field__textarea {
  appearance: none;
  background: transparent;
  border: 0;
  border-bottom: 1px solid #999;
  color: #333;
  display: block;
  font-size: 1.2rem;
  margin-top: 24px;
  outline: 0;
  padding: 0 12px 10px 12px;
  width: 100%;

  // IE 10-11
  &:-ms-input-placeholder {
    ~ .form-field__label {
      @include label-inactive();
    }
  }
  // All other browsers except Edge
  &:placeholder-shown {
    ~ .form-field__label {
      @include label-inactive();
    }
  }
  &:focus {
    ~ .form-field__label {
      color: #b11adc;
      @include label-active();
    }
    ~ .form-field__bar {
      border-bottom: 2px solid $primary-color;
      transform: scaleX(150);
    }
  }
}
.form-field__textarea {
  height: 150px;
} 
```

我不能说不用 Javascript 就能实现这一点是否值得，但不管怎样，这是一个有趣的实验，可以让我们知道如何去做。在 Javascript 中处理字段状态会产生稍微干净的 HTML 和更容易理解的样式声明。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2018/how-to-create-material-design-like-form-text-fields)