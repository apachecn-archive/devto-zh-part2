# 在 Elm 中嵌入带有定制元素的 React 组件

> 原文：<https://dev.to/norpan/embedding-react-components-in-elm-with-custom-elements-28n1>

Elm 很棒，但是有时候有些东西存在于 Elm 生态系统之外，你想使用它们。在这种情况下，我们需要一个基于 [JSON 模式](https://json-schema.org)的动态表单生成器。虽然在 Elm 中写这样的东西当然是可能的，但是我们只是想要一些可以工作的东西。

有很多动态表单构建器，但是 [react-jsonschema-form](https://github.com/mozilla-services/react-jsonschema-form) 有一个简单的 API，目前对我们来说很好。

我没有包括任何截图，因为它真的像 React 版本一样工作，所以在那里看到截图。

# 基本原则

我们已经试验了在 Elm 中嵌入定制元素的不同方法，但是发现下面的原则工作得很好，并且使代码易于阅读和编写:

*   创建一个 javascript 文件和一个相应的 Elm 模块。
*   仅使用属性，而不是特性，与 Elm 中的组件进行通信。
*   保留组件中所有属性的内部副本。
*   不要在组件中保留任何状态，如果可能，在 Elm 中保留所有状态。
*   如果设置了所有强制属性，则在属性更改时呈现组件。
*   仅使用自定义事件从组件与 Elm 通信，并将这些事件绑定到相关的 React 事件。

# 在实践中

那么，这在实践中是如何运作的呢？请参见下面的代码清单。以下是一些实施说明:

*   如您所见，我们在自定义元素中设置了内部属性，例如`this._schema`。
*   你也可以看到，即使你不需要，我们也会使用`this.`。我发现这有助于了解我实际使用的是什么东西。
*   每次设置一个属性时，我们调用`renderForm()`来检查相关属性是否被设置，然后调用`ReactDOM.render()`来创建 React 组件。
*   我们将自定义元素的内部属性绑定到 React 属性。
*   我们将自定义事件调度程序绑定到 React 事件处理程序。请注意，在创建自定义事件时，您需要使用`detail:`字段，任何其他字段都将被删除。
*   在 Elm 中，我们使用生成的`<react-jsonschema-element>`，就像我们使用任何其他 HTML 元素一样。
*   你可以看到我们以`Json.Decode.Value`的形式传递一切。在这种情况下，这就是我们想要的，因为由于它的动态特性，我们在 Elm 中将其保存为`Value`，但是您当然可以将任何 JSON 解码器放在`Html.Events.on`处理程序中，以及将任何 JSON 编码器放在`Html.Attributes.property`调用中。
*   在您的`Main`中，我们让 Elm 作为状态的管理者，所以每当我们得到一个`onChange`时，我们就改变模型中的数据，再次将它传递给组件。这让我们能够与 React 组件的内部状态保持同步。

# 注意

*   自定义元素还不能在所有浏览器中工作。然而，我们发现在您的`index.html`中包含来自[https://github.com/webcomponents/webcomponentsjs](https://github.com/webcomponents/webcomponentsjs)的 polyfill 效果很好。

# 密码

javascript 文件:

```
import React from "react";
import ReactDOM from "react-dom";
import Form from "react-jsonschema-form";

// React jsonschema form custom element
class ReactJsonschemaForm extends HTMLElement {
  set schema(value) {
    this._schema = value;
    this.renderForm();
  }

  set uiSchema(value) {
    this._uiSchema = value;
    this.renderForm();
  }

  set data(value) {
    this._data = value;
    this.renderForm();
  }

  renderForm() {
    // Only render if schema property has been set
    if (this._schema) {
      ReactDOM.render(
        React.createElement(Form,
          {
            schema: this._schema,
            formData: this._data ? this._data : undefined,
            uiSchema: this._uiSchema ? this._uiSchema : undefined,
            onChange: this.sendChange.bind(this),
            onSubmit: this.sendSubmit.bind(this)
          },
          React.createElement('div', null, [
            React.createElement('button', { key: "submit", type: "submit" }, "Submit"),
            React.createElement('button', { key: "cancel", type: "button", onClick: this.sendCancel.bind(this) }, "Cancel")
          ])
        ),
        this
      );
    }
  }

  sendChange(change) {
    this.dispatchEvent(new CustomEvent('form-change', { detail: change.formData }));
  }

  sendSubmit(change) {
    this.dispatchEvent(new CustomEvent('form-submit', { detail: change.formData }));
  }

  sendCancel() {
    this.dispatchEvent(new CustomEvent('form-cancel'));
  }
}
customElements.define('react-jsonschema-form', ReactJsonschemaForm); 
```

Elm 模块:

```
module ReactJsonschemaForm exposing (view)

import Html
import Html.Attributes
import Html.Events
import Json.Decode

view :
    { schema : Json.Decode.Value
    , uiSchema : Json.Decode.Value
    , data : Json.Decode.Value
    , onChange : Json.Decode.Value -> msg
    , onSubmit : Json.Decode.Value -> msg
    , onCancel : msg
    }
    -> Html.Html msg
view { onChange, onSubmit, onCancel, schema, uiSchema, data } =
    Html.node "react-jsonschema-form"
        [ Html.Attributes.property "uiSchema" uiSchema
        , Html.Attributes.property "data" data
        , Html.Attributes.property "schema" schema
        , Html.Events.on "form-change" (Json.Decode.field "detail" Json.Decode.value |> Json.Decode.map onChange)
        , Html.Events.on "form-submit" (Json.Decode.field "detail" Json.Decode.value |> Json.Decode.map onSubmit)
        , Html.Events.on "form-cancel" (Json.Decode.succeed onCancel)
        ]
        [] 
```

主文件中有趣的部分:

```
module Main exposing (main)

import Json.Decode
import ReactJsonschemaForm

type Model =
    { data: Json.Decode.Value
    , schema: Json.Decode.Value
    , uiSchema: Json.Decode.Value
    , ...
    }

type Msg
    = FormChanged Json.Decode.Value
    | FormSubmitted Json.Decode.Value
    | FormCancelled

update msg model =
    case msg of
        FormChanged data ->
            ( { model | data = data }, Cmd.none )
        FormSubmitted data ->
            ( model, sendDataCmd data )
        ...

view model =
    ReactJsonschemaForm.view
        { schema = model.schema
        , uiSchema = model.uiSchema
        , data = model.data
        , onChange = FormChanged
        , onSubmit = FormSubmitted
        , onCancel = FormCancelled
        } 
```