# 刺激组件:输入字段的计算器

> 原文：<https://dev.to/tomk32/stimulus-component-calculator-for-input-fields-5hi8>

这里有一个我为 [Budget Fox](https://budget-fox.com?utm_source=dev.to) 写的小 stimulusjs 组件。

它使用像 2 + 10/3
这样的基本算法计算输入的值，并在`result`目标中显示结果。

当退出输入或提交表单时，输入的值
被替换为计算值。:w

如果设置了`result`目标，当用户输入数字时，其`innerText`将被替换为
值，并在离开输入时恢复。

```
<div data-controller="input-calculator">
  <label for="amount" data-target="input-calculator.result">Amount</label>
  <input name="amount" data-action="keydown->input-calculator#calculate keyup->input-calculator#calculate blur->input-calculator#calculate" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

data-action 有三个事件监听器，仅仅是因为 stimulus 坚持认为
应该监听什么事件。我们需要更多来更新击键(keyup)
的结果，并在用户点击 enter 提交表单(keydown)
时更新它

```
import { Controller } from "stimulus"

export default class extends Controller
  @targets: ['result']

  connect: ->
    if @resultTarget && @resultTarget.dataset
      @flipResult = true
      @resultTarget.dataset['oldInnerHTML'] = @resultTarget.innerHTML

  calculate: (event) ->
    val = event.target.value
    if @flipResult
      @resultTarget.innerHTML = @resultTarget.dataset['oldInnerHTML']
    if val.match(/[+\-\*\/]/)
      try
        val = eval(val)
        if event.type == 'blur' || event.code == "Enter" || event.keyCode == 13 || event.which == 13
          event.target.value = val
        else if @flipResult
          @resultTarget.innerText = val 
```

Enter fullscreen mode Exit fullscreen mode

代码当然是麻省理工学院许可的。