# 向 StimulusJS 控制器传递参数

> 原文：<https://dev.to/briankephart/passing-arguments-to-stimulusjs-controllers-1kgi>

本周，我将 StimulusJS 安装到我正在开发的 Rails 应用程序中。到目前为止，它似乎很适合，帮助我组织和重用 Javascript 函数，而没有大型框架的麻烦和开销。我本质上是一个 Rubyist，所以这种最简单的 Javascript 方法比其他一些目前流行的方法更适合我。至少，这是我用了几天后的怀疑。

如果你没有使用过 StimulusJS，它围绕着用 Javascript 编写的控制器。这些控制器访问由数据属性指定的 HTML 元素:

*   `data-controller`表示哪个控制器包含相关的 JS 代码，
*   `data-target`表示 HTML 元素与 JS 代码相关，并为其命名，
*   `data-action`表示该元素的变化会触发特定的控制器动作。

这是一个类似于我昨天编写的例子。我们的业务使用多种计费合同类型，每种类型都有自己的月费用。有一个合同类型模型包含该类型合同的默认成本。在我制作新合同的表单中，每当选择新的`ContractType`时，我希望`Contract`的`monthly_cost`变成`ContractType`的`default_cost`。

```
<!-- contracts/new.html.erb (not working) -->

<form data-controller="contracts">
  <select name="contract[contract_type]" id="contract_contract_type"
  data-target="contracts.contractType" data-action="contracts#updateCost">
    <!-- options for each contract type, containing name & id -->
  </select>
  <input type="number" name="contract[monthly_cost]" id="contract_monthly_cost"
  data-target="contracts.monthlyCost">
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这里是刺激合同控制器:

```
// contracts_controller.js (not working)

import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "contractType", "monthlyCost" ]
  updateCost() {
    const contractTypeId = String(this.contractTypeTarget.value) // value from select box
    const monthlyCost = this.monthlyCostTarget // field to fill in
    const contractTypeCost = 'Uh-oh' // How do we get contractType's default cost?
    monthlyCost.value = Number(contractTypeCost).toFixed(2)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的几件事:

*   HTML 中的`data-target`元素在控制器的 targets 数组中列出，该数组的每个元素都可以通过在元素名称上添加`Target`后缀在类中使用。例如，`data-target="contracts.contractType"`在契约控制器的`target`数组中被列为`contractType`，然后在 JS 代码中被访问为`this.contractTypeTarget`。
*   Stimulus 监视具有`data-action`属性的 DOM 元素的变化，因此当选择框的值发生变化时，contracts 控制器将执行`updateCost()`函数。
*   有一个问题。每个`ContractType`的`default_cost`存储在数据库中，客户端 Javascript 代码不能立即访问。选择框只包含每个选项的`name`和`id`。我该如何解决这个问题？

我尝试做的第一件事是通过 data-action 属性传递括号参数。这不起作用。刺激计划不是用来这样使用的。

我考虑过设置一个 API 调用来查询数据库，但是这看起来工作量很大。框架的主要目的是使事情变得更简单，向尚不存在的 API 端点发出额外的 HTTP 请求并不是一个简单的解决方案。

谢天谢地，有一个更简单的方法。我选择将相关的定价信息添加到 select 元素的数据集中。

首先，我将 Rails 控制器中的数据准备为一个 JSON 对象:

```
# contracts_controller.rb

# Create JSON object
# key = contract_type.id, value = contract_type.default_cost
@contract_types = ContractType.all.order :name
@contract_type_data = @contract_types.pluck(:id, :default_cost).to_h.to_json 
```

Enter fullscreen mode Exit fullscreen mode

...然后我将它作为 data-info 属性添加到选择框中(data-info 没有什么特别的；任何未使用的数据属性都可以):

```
<!-- contracts/new.html.erb -->

<form data-controller="contracts">
  <select name="contract[contract_type]" id="contract_contract_type"
  data-target="contracts.contractType" data-action="contracts#updateCost"
  data-info="<%= @contract_type_data %>">
    <!-- options for each contract type -->
  </select>
  <input type="number" name="contract[monthly_cost]" id="contract_monthly_cost"
  data-target="contracts.monthlyCost">
</form> 
```

Enter fullscreen mode Exit fullscreen mode

...最后，这里是刺激控制器:

```
// contracts_controller.js

import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "contractType", "monthlyCost" ]
  updateCost() {
    const contractTypeId = String(this.contractTypeTarget.value) // value from select box
    const contractTypesInfo = JSON.parse(this.contractTypeTarget.dataset.info) // data object
    const monthlyCost = this.monthlyCostTarget // field to fill in
    const contractTypeCost = contractTypesInfo[contractTypeId]
    monthlyCost.value = Number(contractTypeCost).toFixed(2)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当用户在选择框中选择一个`ContractType`时，该框的值被用作访问包含正确`default_cost`的 JSON 对象的键。

这个解决方案有什么问题吗？从我的角度来看不是这样，尽管一个足够大的数据对象被塞进 HTML data-info 属性看起来很奇怪。如果涉及到巨大的数据对象或敏感数据，那么对数据库进行更窄范围的查询的 API 调用可能会更好，以便更具体地了解哪些数据到达了客户端。然而，在这种情况下，我对这个解决方案以及总体刺激措施感到满意。