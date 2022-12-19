# 同时选中和下载 checkbox

> [https://dev . to/juananuiz/标记-y-取消标记-复选框-同时-con-jquery-523 或](https://dev.to/juananruiz/marcar-y-desmarcar-checkbox-de-manera-simultanea-con-jquery-523o)

前几天我不得不做一个表格，把邮件发送到一个封闭的名单上，但他们要求我把邮件发送给所有人或封闭名单的一部分。

类似下图所示的内容

[![Formulario con checks multiples](../Images/5cfd240141b416a87a473afedc760222.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zL6Sh21m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://galatar.com/wp-content/uploads/2018/08/captura_formulario_correo_con_checks_multiples-1024x512.png)

我在一个较早的项目中也做了类似的事情，我决定采用剪切/粘贴技术，但令我惊讶的是，脚本第一次工作，然后拒绝重复这项工作。

我将示例 HTML 片段和开始脚本放在这里，以便对问题进行注释。

```
<table border="1">
  <tr>
    <th><input type="checkbox" id="selectall"></th>
    <th>Participantes</th>
  </tr>
  <tr>
    <td><input type="checkbox" class="case" name="case[]" value="1"></td>
    <td>Fulano de Tal y Cual</td>
  </tr>
  <tr>
    <td><input type="checkbox" class="case" name="case[]" value="2"></td>
    <td>Irene Sal de la Cueva</td>
  </tr>
  <tr>
    <td><input type="checkbox" class="case" name="case[]" value="3"></td>
    <td>Zutano Ruiz Ruiz</td>
  </tr>
</table>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script>
  $("#selectall").on("click", function() {
    $(".case").attr("checked", this.checked);
  });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我设置了一个标记为“selectall”的第一个复选框，以选中或取消选中我用“case”类标记的其馀复选框，以便可以一起引用它们。

当您按一下上方的核取方块时，所有其他核取方块都会变更状态。此程式码的缺点是，在旧版 jQuery 中，attr 函数只会在载入页面时修改元素的原始属性，而不会在修改页面后修改。在任何情况下都必须使用 prop 函数。

```
$("#selectall").on("click", function() {  
  $(".case").prop("checked", this.checked);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

进一步增强了脚本的功能我添加了另一个事件，这样当取消选中一个或多个项目时，顶部的复选框也将被取消选中。如果每个人都有标记，那么上级也有标记。

```
$("#selectall").on("click", function() {  
  $(".case").prop("checked", this.checked);  
});  

// if all checkbox are selected, check the selectall checkbox and viceversa 
$(".case").on("click", function() {  
  if ($(".case").length == $(".case:checked").length) {  
    $("#selectall").prop("checked", true);  
  } else {  
    $("#selectall").prop("checked", false);  
  }  
}); 
```

Enter fullscreen mode Exit fullscreen mode

我希望有一天能帮上忙。不管怎么说，我把编码留给你们这样你们就可以和它玩了。